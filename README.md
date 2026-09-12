<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

<script>
/* =========================================================
   BTReDS SQUAD FLASH PEAK
   SUPABASE AUTH + GOOGLE OAUTH
   ========================================================= */

const SUPABASE_URL =
  "https://oztmylktxjymmpkzroim.supabase.co";

const SUPABASE_KEY =
  "sb_publishable_vaaNcr7zd-2M1ebHAN_4oQ_pAZzHVYb";

const db = supabase.createClient(
  SUPABASE_URL,
  SUPABASE_KEY,
  {
    auth: {
      persistSession: true,
      autoRefreshToken: true,
      detectSessionInUrl: true,
      flowType: "pkce"
    }
  }
);


/* =========================================================
   ELEMENT LOGIN
   ========================================================= */

const loginPage = document.getElementById("loginPage");
const appPage   = document.getElementById("appPage");
const err        = document.getElementById("err");


/* =========================================================
   PESAN ERROR
   ========================================================= */

function showError(message) {
  console.error("BTReDS AUTH:", message);

  if (err) {
    err.textContent = message;
    err.style.display = "block";
  }
}


/* =========================================================
   TAMPILKAN LOGIN
   ========================================================= */

function showLogin() {

  if (loginPage) {
    loginPage.style.display = "";
  }

  if (appPage) {
    appPage.style.display = "none";
  }
}


/* =========================================================
   TAMPILKAN DASHBOARD
   ========================================================= */

function showApp() {

  if (loginPage) {
    loginPage.style.display = "none";
  }

  if (appPage) {
    appPage.style.display = "";
  }
}


/* =========================================================
   GOOGLE LOGIN
   ========================================================= */

async function loginGoogle() {

  try {

    if (err) {
      err.textContent = "";
      err.style.display = "none";
    }

    console.log("Memulai Google Login...");

    const redirectTo =
      window.location.origin +
      window.location.pathname;

    console.log("Redirect:", redirectTo);

    const { data, error } =
      await db.auth.signInWithOAuth({

        provider: "google",

        options: {

          redirectTo: redirectTo,

          queryParams: {
            access_type: "offline",
            prompt: "select_account"
          }

        }

      });


    if (error) {
      console.error("Google OAuth Error:", error);
      showError(error.message);
      return;
    }

    console.log("Google OAuth dimulai:", data);

  }

  catch (e) {

    console.error("Google Login Exception:", e);

    showError(
      "Login Google gagal: " + e.message
    );

  }

}


/* =========================================================
   CEK SESSION
   ========================================================= */

async function checkAuth() {

  try {

    console.log("Memeriksa session Supabase...");

    const {
      data,
      error
    } = await db.auth.getSession();


    if (error) {

      console.error(
        "getSession error:",
        error
      );

      showLogin();
      return;

    }


    const session = data.session;


    if (!session) {

      console.log(
        "Tidak ada session."
      );

      showLogin();
      return;

    }


    console.log(
      "Session ditemukan:",
      session.user.email
    );


    await loadUserProfile(
      session.user
    );

  }

  catch (e) {

    console.error(
      "checkAuth error:",
      e
    );

    showLogin();

  }

}


/* =========================================================
   LOAD PROFILE
   ========================================================= */

async function loadUserProfile(user) {

  try {

    console.log(
      "Mencari profile:",
      user.id
    );


    const {
      data: profile,
      error
    } = await db
      .from("profiles")
      .select("*")
      .eq("id", user.id)
      .maybeSingle();


    if (error) {

      console.error(
        "Profile error:",
        error
      );

      showError(
        "Gagal mengambil profile: " +
        error.message
      );

      showLogin();

      return;

    }


    /* -----------------------------------------
       GOOGLE USER BELUM PUNYA PROFILE
       ----------------------------------------- */

    if (!profile) {

      console.log(
        "Profile belum ada. Membuat profile..."
      );


      const googleName =
        user.user_metadata?.full_name ||
        user.user_metadata?.name ||
        user.email?.split("@")[0] ||
        "Player";


      const {
        data: newProfile,
        error: insertError
      } = await db
        .from("profiles")
        .insert({

          id: user.id,

          full_name: googleName,

          role: "player"

        })
        .select()
        .single();


      if (insertError) {

        console.error(
          "Gagal membuat profile:",
          insertError
        );

        showError(
          "Akun Google berhasil login, tetapi profile belum dapat dibuat. " +
          insertError.message
        );

        showLogin();

        return;

      }


      console.log(
        "Profile berhasil dibuat:",
        newProfile
      );


      window.currentUser =
        user;

      window.currentProfile =
        newProfile;


      showApp();


      if (
        typeof initApp === "function"
      ) {

        await initApp(
          user,
          newProfile
        );

      }


      return;

    }


    /* -----------------------------------------
       PROFILE SUDAH ADA
       ----------------------------------------- */

    console.log(
      "Profile ditemukan:",
      profile
    );


    window.currentUser =
      user;

    window.currentProfile =
      profile;


    showApp();


    if (
      typeof initApp === "function"
    ) {

      await initApp(
        user,
        profile
      );

    }

  }

  catch (e) {

    console.error(
      "loadUserProfile error:",
      e
    );

    showError(
      "Terjadi kesalahan saat memuat akun: " +
      e.message
    );

    showLogin();

  }

}


/* =========================================================
   AUTH STATE LISTENER
   ========================================================= */

db.auth.onAuthStateChange(
  async (event, session) => {

    console.log(
      "AUTH EVENT:",
      event
    );


    if (
      event === "SIGNED_IN" &&
      session
    ) {

      console.log(
        "SIGNED_IN:",
        session.user.email
      );


      /*
       * Jangan langsung redirect.
       * Session sudah diberikan Supabase.
       */

      setTimeout(
        async () => {

          await loadUserProfile(
            session.user
          );

        },
        100
      );

    }


    if (
      event === "SIGNED_OUT"
    ) {

      console.log(
        "SIGNED_OUT"
      );

      window.currentUser =
        null;

      window.currentProfile =
        null;

      showLogin();

    }

  }
);


/* =========================================================
   LOGOUT
   ========================================================= */

async function logout() {

  try {

    const {
      error
    } = await db.auth.signOut();


    if (error) {

      console.error(
        "Logout error:",
        error
      );

      showError(
        error.message
      );

      return;

    }


    window.currentUser =
      null;

    window.currentProfile =
      null;


    showLogin();

  }

  catch (e) {

    console.error(
      "Logout exception:",
      e
    );

  }

}


/* =========================================================
   START WEBSITE
   ========================================================= */

async function startBTReDS() {

  console.log(
    "BTReDS website starting..."
  );


  /*
   * Supabase akan membaca token OAuth
   * dari URL setelah kembali dari Google.
   */

  await checkAuth();

}


/* =========================================================
   JALANKAN SETELAH HALAMAN SELESAI
   ========================================================= */

if (
  document.readyState === "loading"
) {

  document.addEventListener(
    "DOMContentLoaded",
    startBTReDS
  );

} else {

  startBTReDS();

}

</script>
