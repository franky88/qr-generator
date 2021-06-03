<template>
  <div class="row">
    <div class="col-sm-4">
      <div class="card card-body mb-2">
        <div
          v-if="successMsg"
          class="alert alert-success d-flex align-items-center"
          role="alert"
        >
          <!-- <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Success:"><use xlink:href="#check-circle-fill"/></svg> -->
          <div><i class="bi bi-check-circle-fill"></i> {{ successMsg }}</div>
        </div>
        <div
          v-if="inputErr"
          class="alert alert-danger d-flex align-items-center"
          role="alert"
        >
          <!-- <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Success:"><use xlink:href="#check-circle-fill"/></svg> -->
          <div>
            <i class="bi bi-exclamation-triangle-fill"></i>
            <strong> Oh!</strong> {{ inputErr }}
          </div>
        </div>
        <strong class="card-title">AOR QR Generator Form</strong>
        <!-- <p v-show="busNameErr" style="color:red;"><small><i class="bi bi-arrow-down"></i> {{busNameErr}} *</small></p> -->
        <div class="form-floating mb-2">
          <input
            v-model="busName"
            type="text"
            class="form-control form-control-sm"
            id="businessName"
            placeholder="AdOn Group"
          />
          <label for="businessName" class="form-label">Business Name</label>
          <!-- <input type="text" id="businessName"> -->
        </div>
        <!-- <div v-show="rLinkErr" class="alert alert-warning alert-dismissible fade show" role="alert">
              <strong>Oh snap!</strong> {{ rLinkErr }}.
              <button type="button" class="btn-close btn-sm" data-bs-dismiss="alert" aria-label="Close"></button>
            </div> -->
        <!-- <p v-show="busNameErr" style="color:red;"><small><i class="bi bi-arrow-down"></i> {{busNameErr}} *</small></p> -->
        <div class="form-floating mb-2">
          <input
            type="url"
            class="form-control form-control-sm"
            id="review-link"
            :placeholder="opt.text"
            v-model="rLink"
          />
          <label for="reviewlink" class="form-label">Review link</label>
        </div>
        <div class="form-floating mb-2">
          <select v-model="reviewFor" class="form-select" id="reviewfor">
            <option value="Google" selected>Google</option>
            <option value="Google or Facebook">Google or Facebook</option>
            <option value="Facebook">Facebook</option>
          </select>
          <label for="floatingSelect">Review type</label>
        </div>
        <label for="logoimage" class="form-label">Company logo</label>
        <div class="mb-3 input-group">
          <input
            type="file"
            class="form-control form-control-sm"
            id="upImg"
            name="image"
            accept="image/png, image/jpeg"
            aria-describedby="uplFile"
            aria-label="Upload"
            @change="getImageBase64"
          />
          <button
            class="btn btn-outline-primary btn-sm"
            type="button"
            id="uplFile"
            @click="uploadImg"
          >
            Upload
          </button>
        </div>
        <div v-show="imageBase64" class="card mb-3" style="max-width: 540px;">
          <div class="row g-0">
            <div class="col-md-4">
              <img
                class="align-self-center"
                id="output"
                :src="image.src"
                :alt="imageName"
              />
            </div>
            <div class="col-md-8">
              <div class="card-body">
                <p class="card-title">
                  {{ imageName }} |
                  <small class="text-muted" v-show="imageBase64"
                    >File size: {{ imageSize }} KB</small
                  >
                </p>
                <!-- <a :href="image.src" target="_blank"><small class="text-muted">View Image</small></a> -->
                <button
                  v-show="imageBase64"
                  class="btn btn-outline-danger btn-sm"
                  @click="clearImage"
                >
                  Clear
                </button>
              </div>
            </div>
          </div>
        </div>
        <button @click="generateQR" class="btn btn-outline-success btn-sm">
          <i class="bi bi-upc-scan"></i> Generate QR
        </button>
      </div>
    </div>
    <div class="col-sm-8">
      <div class="row">
        <div class="col-sm-4">
          <div class="card card-body mb-2" v-if="genLink != ''">
            <div class="form-check form-switch">
              <input class="form-check-input" type="checkbox" @change="qrOptions = !qrOptions">
              <label class="form-check-label" for="show-qr-options">Show QR Options</label>
            </div>
            <div v-if="qrOptions">
              <hr>
              <div class="form-check">
                <input class="form-check-input" type="checkbox" @change="logoBGTransparent = !logoBGTransparent">
                <label class="form-check-label" for="logo-transparency">Transparent logo</label>
              </div>
              <hr>
              <div v-show="!logoBGTransparent">
                <label for="bgColor" class="form-label">Logo background color</label>
                <input type="color" class="form-control form-control-color" :value="logoBGColor" id="bgColor" title="Choose your color">
                <hr>
              </div>
              <label for="logo-size" class="form-label">Logo scale</label>
              <input v-model="imgScaleValue" type="range" class="form-range" :min="imgScaleMinValue" :max="imgScaleMaxValue" :step="scaleSteps" id="logo-size" @change="imageScaleSize">
              <span class="badge bg-info text-dark">{{ imgScaleValue }}</span>
              <hr>
              <button class="btn btn-primary btn-sm mb-2" @click="updateQR">Update QR</button>
            </div>
          </div>
          <div class="card card-body mb-2" v-show="genLink">
            <strong>Save PDF</strong>
            <hr>
            <strong>Note: </strong>
            <small>Please check the PDF before sending.</small>
            <br>
            <small>This app is not supporting mobile. Update will be available soon.</small>
            <hr>
            <button class="btn btn-outline-primary btn-sm" @click="savePDF">
              <i class="bi bi-save-fill"></i> Save PDF
            </button>
            
            
          </div>
        </div>
        <div class="col-sm-8">
          <div class="card" style="text-align: center;">
            <div class="card-body mb-3">
              <div id="toPDFFile" class="mt-4">
                <h1
                  class="card-title"
                  style="text-align: center;"
                  v-if="busName == ''"
                >
                  Ad On Group
                </h1>
                <h1
                  class="card-title"
                  style="text-align: center;"
                  v-else-if="busName"
                >
                  {{ busName }}
                </h1>
                <h4 style="text-align: center;">values your feedback.</h4>
                <br />
                <div id="qrcode" style="text-align: center;">
                  <!-- <img :src="image.src" alt="" style="width:100px; z-index:20; position: float;"> -->
                </div>
                <br />
                <br />
                <h5 style="text-align: center;">
                  Please scan above to leave us a
                  <br />
                  review on {{ reviewFor }}!
                </h5>
                <br />
                <h4 style="text-align: center;">powered by</h4>
                <div style="text-align: center;">
                  <img
                    src="../assets/AORlogo.png"
                    alt=""
                    style="width: 250px;"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import * as QRCode from "easyqrcodejs";
import * as html2pdf from "html2pdf.js";

export default {
  name: "QR generator",
  data() {
    return {
      isShow: false,
      qrWidth: 300,
      qrHeight: 300,
      imageName: "",
      imageSize: "",
      image: "",
      rLink: "",
      imageBase64: "",
      generatedQRCode: "",
      busName: "",
      inputErr: "",
      reviewFor: "Google",
      opt: "",
      imgFile: "",
      genLink: "",
      rLinkErr: "",
      successMsg: "",
      logoBGColor: "#ffffff",
      qrOptions: false,
      logoBGTransparent: false,
      scaleSteps: 0.01,
      imgScaleMinValue: 0.25,
      imgScaleMaxValue: 0.35,
      imgScaleValue: 0.25,
    };
  },
  mounted() {
    const rLink = document.getElementById("review-link").value;
    this.rLink = rLink;
    const revFor = document.getElementById("reviewfor").value;
    this.reviewFor = revFor;
    //Business name
    const businessName = document.getElementById("businessName").value;
    this.busName = businessName;
    //generated QR output
    const qrcode = document.getElementById("qrcode");
    this.generatedQRCode = qrcode;
    var opt = {
      text: "https://app.adonreview.com.au/review",
      width: this.qrWidth,
      height: this.qrHeight,
    };
    this.opt = opt;
    new QRCode(this.generatedQRCode, opt);
    //image upload
    const img = document.getElementById("output");
    this.image = img;
    //bg color
    
    //qr options
    // const size = document.querySelector("#logo-size").value;
    // console.log(size);
  },
  methods: {
    uploadImg() {
      const upimg = document.getElementById("upImg");
      this.getImageBase64(upimg, (imgb64) => {
        this.imageBase64 = imgb64;
      });
      this.image.src = URL.createObjectURL(upimg.files[0]);
    },
    clearImage() {
      this.image.src = "";
      this.imageBase64 = "";
      this.imageName = "";
      this.imageSize = "";
    },
    getImageBase64(element, callback) {
      let imgb64 = "";
      let file = element.files[0];
      let reader = new FileReader();
      reader.onloadend = function() {
        imgb64 = reader.result;
        callback(imgb64);
        // console.log("Base64 " + imgb64);
      };
      reader.readAsDataURL(file);
      this.imageName = file.name;
      let roundOffSize = Math.round(file.size * 0.001 * 100) / 100;
      this.imageSize = roundOffSize;
    },
    generateQR() {
      const logoWidth = this.qrWidth*this.imgScaleValue;
      const logoHeight = this.qrHeight*this.imgScaleValue;
      if (this.busName === "" || this.rLink === "") {
        this.inputErr = "You missed something!";
      } else {
        this.successMsg = "QR code generated!";
        this.inputErr = "";
        this.genLink = this.rLink;
        let options = {
          text: this.rLink,
          width: this.qrWidth,
          height: this.qrHeight,
          colorDark: "#000000",
          colorLight: "#ffffff",
          correctLevel: QRCode.CorrectLevel.H,
          drawer: "svg",
          dotScale: 1,
          logo: this.imageBase64,
          logoWidth: logoWidth,
          logoHeight: logoHeight,
          logoBackgroundTransparent: false,
          logoBackgroundColor: this.logoBGColor,
        };
        new QRCode(this.generatedQRCode, options);
      }
    },
    imageScaleSize(element){
      const imgScale = element.target.value;
      // const imgScale = document.getElementById("logo-size").value;
      console.log(imgScale);
      this.imgScaleValue = imgScale;
    },
    updateQR(){
      const logoWidth = this.qrWidth*this.imgScaleValue;
      const logoHeight = this.qrHeight*this.imgScaleValue;
      const bgcolor = document.querySelector('#bgColor');
      this.logoBGColor = bgcolor.value;
      if (this.busName === "" || this.rLink === "") {
        this.inputErr = "You missed something! Please check Business name or Review link";
      } else {
        this.successMsg = "QR code generated!";
        this.inputErr = "";
        this.genLink = this.rLink;
        let options = {
          text: this.rLink,
          width: this.qrWidth,
          height: this.qrHeight,
          colorDark: "#000000",
          colorLight: "#ffffff",
          correctLevel: QRCode.CorrectLevel.H,
          drawer: "svg",
          dotScale: 1,
          logo: this.imageBase64,
          logoWidth: logoWidth,
          logoHeight: logoHeight,
          logoBackgroundTransparent: this.logoBGTransparent,
          logoBackgroundColor: this.logoBGColor,
        };
        new QRCode(this.generatedQRCode, options);
      }
    },
    savePDF() {
      var element = document.getElementById("toPDFFile");
      var opt = {
        margin: 0.5,
        filename: this.busName + " QR Code.pdf",
        image: { type: "jpeg", quality: 0.98 },
        html2canvas: { scale: 2 },
        enableLinks: true,
        jsPDF: { unit: "pt", format: [400, 600], orientation: "p" },
      };
      html2pdf()
        .set(opt)
        .from(element)
        .save();
    },
  },
};
</script>
<style scoped>
.dropbox {
  outline: 2px dashed grey; /* the dash box */
  outline-offset: -10px;
  background: lightcyan;
  color: dimgray;
  padding: 10px 10px;
  min-height: 200px; /* minimum height */
  position: relative;
  cursor: pointer;
}
.input-file {
  opacity: 0; /* invisible but it's there! */
  width: 100%;
  height: 200px;
  position: absolute;
  cursor: pointer;
}
.dropbox:hover {
  background: lightblue; /* when mouse over to the drop zone, change color */
}
.dropbox p {
  font-size: 1.2em;
  text-align: center;
  padding: 50px 0;
}
#output {
  width: 100%;
  height: auto;
  padding: 10px;
}
h1 {
  font-family: "Montserrat", sans-serif;
  font-weight: 800;
  font-size: 40px;
  color: #000000
}
h4, h5 {
  font-family: "Montserrat", sans-serif;
  font-weight: 800;
  color: #000000;
}
</style>
