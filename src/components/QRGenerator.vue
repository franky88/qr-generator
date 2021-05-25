<template>
    <div class="row">
        <div class="col-sm-4">
          <div class="card card-body mb-2">
            <h4 class="card-title">AOR QR Generator</h4>
            <div class="mb-3">
                <label for="businessName" class="form-label">Business Name</label>
                <input v-model="busName" type="text" class="form-control form-control-sm" id="businessName">
                <!-- <input type="text" id="businessName"> -->
            </div>
            <div class="mb-3">
                <label for="reviewlink" class="form-label">Review link</label>
                <input type="url" class="form-control form-control-sm" id="review-link" :placeholder="opt.text" v-model="rLink">
            </div>
            <div class="input-group input-group-sm mb-3">
              <label class="input-group-text" for="inputGroupSelect01">Review type</label>
              <select v-model="reviewFor" class="form-select" id="reviewfor">
                <option value="Google" selected>Google</option>
                <option value="Google or Facebook">Google or Facebook</option>
                <option value="Facebook">Facebook</option>
              </select>
            </div>
            <label for="logoimage" class="form-label">Company logo</label>
            <div class="mb-3 input-group">
                <input type="file" class="form-control form-control-sm" id="upImg" name="image" accept="image/png, image/jpeg" aria-describedby="uplFile" aria-label="Upload" @change="getImageBase64">
                <button class="btn btn-outline-primary btn-sm" type="button" id="uplFile" @click="uploadImg">Upload</button>
            </div>
            <div v-show="imageBase64" class="card mb-3" style="max-width: 540px;">
              <div class="row g-0">
                <div class="col-md-4">
                  <img class="align-self-center" id="output" :src="image.src" :alt="imageName">
                </div>
                <div class="col-md-8">
                  <div class="card-body">
                    <p class="card-title">{{imageName}} | <small class="text-muted" v-show="imageBase64">File size: {{imageSize}} KB</small></p>
                    <!-- <a :href="image.src" target="_blank"><small class="text-muted">View Image</small></a> -->
                    <button v-show="imageBase64" class="btn btn-outline-danger btn-sm" @click="clearImage">Clear</button>
                  </div>
                </div>
              </div>
            </div>
            <button @click="generateQR" class="btn btn-outline-success btn-sm"><i class="bi bi-upc-scan"></i> Generate QR</button>
          </div>
          <div class="card card-body">
            <strong>PDF Options</strong>
            <button class="btn btn-outline-primary btn-sm" @click="savePDF"><i class="bi bi-save-fill"></i> Save PDF</button> 
          </div>
        </div>
        <div class="col-sm-8">
            <div class="row">
                <div class="col-sm-12">
                    <div class="card" style="text-align: center;">
                        <div class="card-body">
                            <div id="toPDFFile">
                                <h1 class="card-title" style="text-align: center;">{{ busName }}</h1>
                                <h4 style="text-align: center;">values your feedback.</h4>
                                <br>
                                <div id="qrcode" style="text-align: center;">
                                    <!-- <img :src="image.src" alt="" style="width:100px; z-index:20; position: float;"> -->
                                </div>
                                <br>
                                <br>
                                <h5 style="text-align: center;">
                                    Please scan above to leave us a
                                    <br> review on {{reviewFor}}!
                                </h5>
                                <br>
                                <h4 style="text-align: center;">powered by</h4>
                                <div style="text-align: center;">
                                    <img src="../assets/AORlogo.png" alt="" style="width: 250px;">
                                </div>
                                <br>
                                <br>
                                <div style="text-align: center;">
                                  <a :href="genLink">{{ genLink }}</a>
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
import * as QRCode from 'easyqrcodejs'
import * as html2pdf from 'html2pdf.js'

export default {
  name: 'QR generator',
  data(){
      return {
        isShow: false,
        qrWidth: 300,
        qrHeight: 300,
        imageName: '',
        imageSize: '',
        image: '',
        rLink: '',
        imageBase64: '',
        generatedQRCode: '',
        busName: 'AdOn Group',
        reviewFor: 'Google',
        opt: '',
        imgFile: '',
        genLink: ''
        // bgColor: ''
      }
  },
  mounted () {
    const rLink = document.getElementById("review-link").value;
    this.rLink = rLink
    const revFor = document.getElementById('reviewfor').value;
    this.reviewFor = revFor;
    //Business name
    const businessName = document.getElementById('businessName').value;
    this.busName = businessName;
    //generated QR output
    const qrcode = document.getElementById("qrcode");
    this.generatedQRCode = qrcode;
    var opt = {
      text: 'https://app.adonreview.com.au/review',
      width: this.qrWidth,
      height: this.qrHeight,
    }
    this.opt = opt;
    new QRCode(this.generatedQRCode, opt)
    //image upload
    const img = document.getElementById('output');
    this.image = img
    //bg color
    // const bgcolor = document.getElementById('bgcolor');
    // this.bgColor = bgcolor;
  },
  methods: {
    uploadImg(){
      const upimg = document.getElementById('upImg');
      this.getImageBase64(upimg, (imgb64) => {
        this.imageBase64 = imgb64;
      })
      this.image.src = URL.createObjectURL(upimg.files[0]);
    },
    clearImage(){
      this.image.src = '';
      this.imageBase64 = '';
      this.imageName = '';
      this.imageSize = '';
    },
    getImageBase64(element, callback){
        let imgb64="";
        let file = element.files[0];
        let reader = new FileReader();
        reader.onloadend = function() {
            imgb64 = reader.result
            callback(imgb64);
            console.log("Base64 "+imgb64)
        }
        reader.readAsDataURL(file);
        this.imageName = file.name;
        let roundOffSize = Math.round((file.size*0.001)*100)/100;
        this.imageSize = roundOffSize;
    },
    // changeBackGroundColor(){
    //   this.bgColor = 
    // },
    generateQR(){
        this.genLink = this.rLink
        let options = {
            text: this.rLink,
            width: this.qrWidth,
            height: this.qrHeight,
            colorDark : "#000000",
            colorLight : "#ffffff",
            correctLevel : QRCode.CorrectLevel.H,
            drawer: 'svg',
            dotScale: 1,
            logo: this.imageBase64,
            logoWidth: this.image.width,
            logoHeight: this.image.height,
            logoBackgroundTransparent: false,
            logoBackgroundColor: "#ffffff",
        }
        new QRCode(this.generatedQRCode, options)
    },
    savePDF(){
        var element = document.getElementById('toPDFFile');
        var opt = {
            margin: 0.5,
            filename: this.busName+' QR Code.pdf',
            image: { type: 'jpeg', quality: 0.98 },
            html2canvas: { scale: 2 },
            enableLinks: true,
            jsPDF: { unit: 'in', format: [8.5, 7], orientation: 'portrait' }
        };
        html2pdf().set(opt).from(element).save();
    }
  }
}
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
  #output{
    width: 100%;
    height: auto;
    padding: 10px;
  }
  /* #businessName {
    border-top: none;
    border-right: none;
    border-left: none;
    border-bottom: .5px solid rgba(200,200,100, .5);
    width: 100%;
  }
    #businessName:focus {
    border-top: none;
    border-right: none;
    border-left: none;
    border-bottom: .5px solid rgba(200,200,100, .5);
    width: 100%;
  } */
</style>