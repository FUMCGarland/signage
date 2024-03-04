<script lang="ts">
  import { toasts, ToastContainer, FlatToast } from "svelte-toasts";
  import { Container, Col, Row } from "sveltestrap";
  import Calendar from "./components/Calendar.svelte";
  import _Styles from "./Styles.svelte";

  let gsiStarted = false; // when Auth loaded
  let gapiStarted = false;
  let calendar; // bound to Component
  const backgrounds = [
    "http://fumc01.first.local/images/01.jpg",
    "http://fumc01.first.local/images/02.jpg",
    "http://fumc01.first.local/images/03.jpg",
    "http://fumc01.first.local/images/04.jpg",
    "http://fumc01.first.local/images/05.jpg",
    "http://fumc01.first.local/images/06.jpg",
  ];

  function startGsi() {
    google.accounts.id.initialize({
      auto_select: true,
      client_id:
        "759834632414-sjjhvci6ooeemkdcllln6oj8ajpihk02.apps.googleusercontent.com",
      scope: "https://www.googleapis.com/auth/calendar.readonly",
      callback: authorized,
    });
    google.accounts.id.prompt((notification) => {
      // console.log(notification.getMomentType());
      // this isn't correct, fix later
      gsiStarted = true;
      maybeStart();
    });
  }

  function authorized(result) {
    console.log("authentication status:", result);
    gsiStarted = true;
    maybeStart();
  }

  function startGapi() {
    gapi.load("client", initializeGapiClient);
  }

  async function initializeGapiClient() {
    await gapi.client.init({
      apiKey: "AIzaSyBLepDpTLC_wqi1hi1w90Mhx2IaU0LhwKI",
      discoveryDocs: [
        "https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest",
      ],
    });
    gapiStarted = true;
    maybeStart();
  }

  function maybeStart() {
    // wait until both components are loaded to start
    if (!gapiStarted || !gsiStarted) {
      return;
    }
    console.log("starting up");
    calendar.run();

    // cycle out the background
    setInterval(() => {
      const n = backgrounds[Math.floor(Math.random() * backgrounds.length)];
      document.getElementsByTagName("body")[0].style.backgroundImage =
        "url(" + n + ")";
    }, 300000);
  }
</script>

<svelte:head>
  <title>First United Methodist Church Garland</title>
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css"
  />
  <script
    async
    defer
    src="https://accounts.google.com/gsi/client"
    on:load={startGsi}
  ></script>
  <script
    async
    defer
    src="https://apis.google.com/js/api.js"
    on:load={startGapi}
  ></script>
</svelte:head>

<main>
  <ToastContainer let:data>
    <FlatToast {data} />
  </ToastContainer>

  <Container class="cover-container mx-auto">
    <Row>
      <Col>
        <h1>First United Methodist Church Garland</h1>
      </Col>
    </Row>
    <Row>
      <Col>
        <Calendar bind:this={calendar} />
      </Col>
    </Row>
  </Container>
</main>
