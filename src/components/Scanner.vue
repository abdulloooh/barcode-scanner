<template>
  <section>
    <!-- Just headings -->
    <article>
      <!-- <div class="name">Barcode Scanner Implementation</div> -->

      <div
        ref="descriptionRef"
        id="description"
        class="description"
      >Start the scanner and then aim at a barcode to get the encoded data.</div>

      <div ref="libLoadingRef" id="lib-loading">
        <br />loading, please wait...
      </div>
    </article>

    <!-- Real implementation featuring start button, capturing and result with the barcode symbology -->
    <button
      ref="barcodeStarterRef"
      id="barcode-picker-starter-button"
      v-on:click="startBarcodePicker"
      hidden
    >Start Scanning</button>

    <div ref="barcodeResultRef" id="barcode-result" class="result-text">&nbsp;</div>

    <div ref="barcodePickerRef" id="barcode-picker" class="scanner"></div>
  </section>
</template>

<script>
import * as ScanditSDK from "scandit-sdk";
const key = process.env.VUE_APP_KEY,
  engineLocation = "https://cdn.jsdelivr.net/npm/scandit-sdk@5.x/build";

export default {
  name: "Scanner",
  props: {},
  data() {
    return {
      scanditBarcodePicker: undefined
    };
  },

  mounted() {
    ScanditSDK.CameraAccess.getCameras();
    ScanditSDK.configure(key, { engineLocation })
      .then(() => {
        return ScanditSDK.BarcodePicker.create(this.$refs.barcodePickerRef, {
          scanningPaused: true,
          accessCamera: false,
          visible: false,
          playSoundOnScan: true,
          vibrateOnScan: true
        }).then(barcodePicker => {
          this.scanditBarcodePicker = barcodePicker;
          const scanSettings = new ScanditSDK.ScanSettings({
            enabledSymbologies: [
              "ean8",
              "ean13",
              "upca",
              "upce",
              "code128",
              "code39",
              "itf",
              "qr",
              "data-matrix"
            ],
            searchArea: { x: 0, y: 0.333, width: 1, height: 0.333 }
          });
          barcodePicker.applyScanSettings(scanSettings);
          barcodePicker.on("ready", () => {
            this.$refs.libLoadingRef.hidden = true;
            this.$refs.barcodeStarterRef.hidden = false;
          });
          barcodePicker.on("scan", scanResult => {
            this.$refs.barcodeResultRef.innerHTML = scanResult.barcodes.reduce(
              (string, barcode) => {
                return (
                  string +
                  ScanditSDK.Barcode.Symbology.toHumanizedName(
                    barcode.symbology
                  ) +
                  ": " +
                  barcode.data +
                  "<br>"
                );
              },
              ""
            );
            this.scanditBarcodePicker.pauseScanning();
            setTimeout(() => {
              this.scanditBarcodePicker.resumeScanning();
            }, 10000);
          });
          barcodePicker.on("scanError", function(error) {
            console.error(error);
          });
        });
      })
      .catch(function(error) {
        console.log(error);
      });
  },
  methods: {
    startBarcodePicker() {
      this.$refs.barcodeStarterRef.hidden = this.$refs.descriptionRef.hidden = true;

      this.scanditBarcodePicker.accessCamera().then(() => {
        this.scanditBarcodePicker.setVisible(true);
        this.scanditBarcodePicker.resumeScanning();
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
section {
  background-color: white;
  color: black;
  text-align: center;
  font: 400 2.5vh "Open Sans", sans-serif;
}

.name {
  text-transform: uppercase;
  font-size: 3vh;
  font-weight: bold;
  margin: 4vh;
  margin-top: 18vh;
}

button {
  background-color: #555;
  color: white;
  font-size: 3vh;
  font-family: inherit;
  box-sizing: border-box;
  padding: 2vh 5vh;
  margin-top: 5vh;
  width: 70vw;
  max-width: 500px;
}

.result-text {
  padding: 2vh;
}
.scanner {
  /* margin: auto; */
  width: 100% !important;
  height: 250px;
  /* max-height: 20vh; */
}
</style>
