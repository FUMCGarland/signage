<script lang="ts">
  import { Container, Row, Col, Card, CardHeader, CardBody } from "sveltestrap";

  let events = new Array();

  export function run() {
    updateEvents();
    setInterval(updateEvents, 360000);
  }

  function formatDate(d) {
    const nd = new Date(d);
    return nd.toLocaleString("en-US", {
      weekday: "long",
      month: "long",
      day: "numeric",
      hour: "2-digit",
      minute: "2-digit",
      hour12: true,
    });
  }

  function updateEvents() {
    var request = gapi.client.calendar.events.list({
      calendarId: "staff@firstmethodistgarland.net",
      timeMin: new Date().toISOString(),
      showDeleted: false,
      singleEvents: true,
      maxResults: 10,
      orderBy: "startTime",
    });

    request.execute(function (resp) {
      events = resp.items;
    });
  }
</script>

<Container>
  <Row>
    <Col class="nopadding">
      {#each events as event}
        <Card>
          <CardHeader><h2>{event.summary}</h2></CardHeader>
          <CardBody
            ><h5>{formatDate(event.start.dateTime)}</h5>
            {event.location}</CardBody
          >
        </Card>
      {/each}
    </Col>
  </Row>
</Container>
