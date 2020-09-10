<script>
  import { onMount } from "svelte";
  import * as bodyPix from "@tensorflow-models/body-pix";
  import "@tensorflow/tfjs-backend-webgl";
  import { log } from "@tensorflow/tfjs-core/dist/log";

  let videoFeed;
  let net;
  let maskCanvas;
  let maskCtx;
  let canvasCrop;

  let enableMask = true;

  const foregroundColor = { r: 0, g: 0, b: 0, a: 0 };
  const backgroundColor = { r: 0, g: 255, b: 0, a: 255 };

  async function loadAndUseBodyPix() {
    net = await bodyPix.load({
      architecture: "MobileNetV1",
      outputStride: 16,
      multiplier: 0.5,
      quantBytes: 2,
    });
  }

  async function onFrame() {
    const segmentation = await net.segmentPerson(videoFeed);
    const mask = bodyPix.toMask(
      segmentation,
      foregroundColor,
      backgroundColor,
      false
    );
    bodyPix.drawMask(maskCanvas, videoFeed, mask, enableMask ? 1 : 0, 2, true);
    requestAnimationFrame(onFrame);
  }

  async function initWebcam() {
    if (navigator.mediaDevices.getUserMedia) {
      let stream = await navigator.mediaDevices.getUserMedia({
        video: true,
        audio: false,
      });
      videoFeed.srcObject = stream;

      return new Promise((resolve) => {
        videoFeed.onloadedmetadata = () => {
          videoFeed.width = videoFeed.videoWidth;
          videoFeed.height = videoFeed.videoHeight;

          maskCanvas.width = videoFeed.width;
          maskCanvas.height = videoFeed.height;

          const cropOffset = 5;
          canvasCrop.style.width = `${videoFeed.width - cropOffset * 2}px`;
          maskCanvas.style.marginLeft = `-${cropOffset}px`;
          canvasCrop.style.height = `${videoFeed.height - cropOffset * 2}px`;
          maskCanvas.style.marginTop = `-${cropOffset}px`;
          resolve();
        };
      });
    }
  }

  onMount(async () => {
    await loadAndUseBodyPix();
    await initWebcam();
    maskCtx = maskCanvas.getContext("2d");
    await onFrame();
  });
</script>

<style>
  #video {
    -moz-transform: scaleX(-1);
    -o-transform: scaleX(-1);
    -webkit-transform: scaleX(-1);
    transform: scaleX(-1);
    display: none;
  }

  #canvas_crop {
    display: inline-block;
    overflow: hidden;
    border: 5px rgb(0, 255, 0) solid;
  }
</style>

<main>
  Enable Mask: <input type="checkbox" bind:checked={enableMask} />
  <br />
  <div id="canvas_crop" bind:this={canvasCrop}>
    <canvas id="mask_canvas" bind:this={maskCanvas} />
  </div>
  <video autoplay="true" bind:this={videoFeed} id="video" />
</main>
