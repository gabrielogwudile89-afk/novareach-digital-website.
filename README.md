
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Novareach Digital Live</title>
  <style>
    body {
      margin: 0;
      padding: 20px;
      background: #10131b;
      color: white;
      font-family: Arial, sans-serif;
      text-align: center;
    }
    .container {
      max-width: 650px;
      margin: auto;
    }
    video {
      width: 100%;
      background: #000;
      border-radius: 16px;
      aspect-ratio: 9 / 16;
      object-fit: cover;
    }
    button {
      padding: 13px 18px;
      margin: 8px 4px;
      border: 0;
      border-radius: 9px;
      font-weight: bold;
      cursor: pointer;
    }
    .start { background: #24d6a0; color: #10131b; }
    .stop { background: #ff5c68; color: white; }
    #status { color: #b8c2d8; }
  </style>
</head>
<body>
  <div class="container">
    <h1>🔴 Novareach Live</h1>
    <p>Preview your camera before going live.</p>

    <video id="camera" autoplay playsinline muted></video>

    <p id="status">Camera is off.</p>

    <button class="start" id="start">Start Camera</button>
    <button class="stop" id="stop">Stop Camera</button>

    <p>Live broadcasting to other users is not connected yet.</p>
    <a href="/" style="color:#24d6a0">Back to homepage</a>
  </div>

  <script>
    const video = document.getElementById("camera");
    const status = document.getElementById("status");
    let stream = null;

    document.getElementById("start").onclick = async () => {
      try {
        if (!navigator.mediaDevices?.getUserMedia) {
          throw new Error("Open this page using HTTPS in a supported browser.");
        }

        stream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: "user" },
          audio: true
        });

        video.srcObject = stream;
        status.textContent = "Camera preview is running.";
      } catch (error) {
        status.textContent = "Could not start camera: " + error.message;
      }
    };

    document.getElementById("stop").onclick = () => {
      if (stream) {
        stream.getTracks().forEach(track => track.stop());
        stream = null;
      }
      video.srcObject = null;
      status.textContent = "Camera is off.";
    };

    window.addEventListener("pagehide", () => {
      if (stream) stream.getTracks().forEach(track => track.stop());
    });
  </script>
</body>
</html>
import {
  LocalParticipant,
  LocalTrackPublication,
  Participant,
  RemoteParticipant,
  RemoteTrack,
  RemoteTrackPublication,
  Room,
  RoomEvent,
  Track,
  VideoPresets,
} from 'livekit-client';

// creates a new room with options
const room = new Room({
  // automatically manage subscribed video quality
  adaptiveStream: true,

  // optimize publishing bandwidth and CPU for published tracks
  dynacast: true,

  // default capture settings
  videoCaptureDefaults: {
    resolution: VideoPresets.h720.resolution,
  },
});

// get your url from livekit's dashboard, or point it at a self hosted livekit deployment
const url = "ws://localhost:7800";

// generate a token by making a request to a endpoint using the livekit server sdk or
// using a prebuilt TokenSource (documented below)
const token = "...";

// pre-warm connection, this can be called as early as your page is loaded
room.prepareConnection(url, token);

// set up event listeners
room
  .on(RoomEvent.TrackSubscribed, handleTrackSubscribed)
  .on(RoomEvent.TrackUnsubscribed, handleTrackUnsubscribed)
  .on(RoomEvent.ActiveSpeakersChanged, handleActiveSpeakerChange)
  .on(RoomEvent.Disconnected, handleDisconnect)
  .on(RoomEvent.LocalTrackUnpublished, handleLocalTrackUnpublished);

// connect to room
await room.connect(url, token);
console.log('connected to room', room.name);

// publish local camera and mic tracks
await room.localParticipant.enableCameraAndMicrophone();

function handleTrackSubscribed(
  track: RemoteTrack,
  publication: RemoteTrackPublication,
  participant: RemoteParticipant,
) {
  if (track.kind === Track.Kind.Video || track.kind === Track.Kind.Audio) {
    // attach it to a new HTMLVideoElement or HTMLAudioElement
    const element = track.attach();
    parentElement.appendChild(element);
  }
}

function handleTrackUnsubscribed(
  track: RemoteTrack,
  publication: RemoteTrackPublication,
  participant: RemoteParticipant,
) {
  // remove tracks from all attached elements
  track.detach();
}

function handleLocalTrackUnpublished(
  publication: LocalTrackPublication,
  participant: LocalParticipant,
) {
  // when local tracks are ended, update UI to remove them from rendering
  publication.track.detach();
}

function handleActiveSpeakerChange(speakers: Participant[]) {
  // show UI indicators when participant is speaking
}

function handleDisconnect() {
  console.log('disconnected from room');
}
