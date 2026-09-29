```vue
<template>
  <ion-page>

    <!-- HEADER -->
    <ion-header class="app-header">
      <ion-toolbar>

        <div class="header-content">

          <div class="brand">

            <div>
              <div class="brand-name">
                ProjSec
              </div>

              <div class="brand-subtitle">
                Secure Text
              </div>
            </div>
          </div>

          <div class="header-heart">
            ♡
          </div>

        </div>
      </ion-toolbar>
    </ion-header>


    <ion-content>

      <div class="page-container">

        <div class="hero-card">

          <div class="hero-content">

            <div class="small-title">
              HELLO, LAYII!
            </div>

            <h1>
              Protect Your Text
            </h1>

            <p>
              Choose a cipher, type your message,
              and keep your text safe!
            </p>

          </div>

        </div>


        <!-- MAIN CARD -->
        <div class="main-card">

          <!-- METHOD -->
          <div class="form-section">

            <div class="section-heading">

              <div class="step-circle">
                1
              </div>

              <div>
                <h2>
                  Choose Your Cipher
                </h2>

                <p>
                  Select how you want to protect your text.
                </p>
              </div>

            </div>


            <select
              v-model="encryptionMethod"
              class="cute-select"
            >

              <option value="AES">
                AES Encryption
              </option>

              <option value="Caesar">
                Caesar Cipher
              </option>

            </select>

          </div>


          <!-- TEXT -->
          <div class="form-section">

            <div class="section-heading">

              <div class="step-circle">
                2
              </div>

              <div>
                <h2>
                  Your Message
                </h2>

                <p>
                  Type the text you want to encrypt.
                </p>
              </div>

            </div>


            <textarea
              v-model="inputText"
              class="cute-textarea"
              placeholder="Write something here..."
              rows="6"
            ></textarea>

          </div>


          <!-- KEY / SHIFT -->
          <div class="form-section">

            <div class="section-heading">

              <div class="step-circle">
                3
              </div>

              <div>

                <h2>
                  {{
                    encryptionMethod === 'AES'
                      ? 'Secret Key'
                      : 'Shift Number'
                  }}
                </h2>

                <p>
                  {{
                    encryptionMethod === 'AES'
                      ? 'Enter your secret encryption key.'
                      : 'Choose how many letters to shift.'
                  }}
                </p>

              </div>

            </div>


            <!-- AES -->
            <input
              v-if="encryptionMethod === 'AES'"
              v-model="encryptionKey"
              type="password"
              class="cute-input"
              placeholder="Enter your secret key..."
            />


            <!-- CAESAR -->
            <input
              v-else
              v-model.number="shiftValue"
              type="number"
              class="cute-input"
              placeholder="Example: 3"
            />

          </div>


          <!-- BUTTONS -->
          <div class="button-container">

            <button
              class="cute-button encrypt-button"
              @click="encryptText"
            >
              Encrypt
            </button>


            <button
              class="cute-button decrypt-button"
              @click="decryptText"
            >
              Decrypt
            </button>

          </div>


          <!-- ERROR -->
          <div
            v-if="errorMessage"
            class="error-box"
          >

            <span>
              {{ errorMessage }}
            </span>

          </div>


          <!-- RESULT -->
          <div
            ref="resultBox"
            class="result-box"
          >

            <div class="result-title">

              <div>

                <h2>
                  Your Result
                </h2>

                <p>
                  Your encrypted or decrypted text appears here.
                </p>

              </div>

            </div>


            <textarea
              v-model="result"
              class="result-textarea"
              rows="6"
              readonly
              placeholder="Your result will appear here..."
            ></textarea>

          </div>


          <!-- CLEAR -->
          <button
            class="clear-button"
            @click="clearAll"
          >
            Clear Everything
          </button>


        </div>


        <!-- FOOTER -->
        <div class="footer">

          <span>
            Made with ♡ using ProjSec
          </span>

        </div>

      </div>

    </ion-content>

  </ion-page>
</template>


<script setup lang="ts">

import { ref, nextTick } from 'vue'
import CryptoJS from 'crypto-js'


const inputText = ref('')
const encryptionKey = ref('')
const result = ref('')
const errorMessage = ref('')

const encryptionMethod = ref('AES')
const shiftValue = ref(3)


// RESULT SECTION REFERENCE
const resultBox = ref<HTMLElement | null>(null)


// SCROLL TO RESULT
async function scrollToResult() {

  await nextTick()

  resultBox.value?.scrollIntoView({
    behavior: 'smooth',
    block: 'start'
  })

}


// ENCRYPT
async function encryptText() {

  errorMessage.value = ''

  if (!inputText.value.trim()) {

    errorMessage.value =
      'Please enter text to encrypt.'

    return
  }


  // AES
  if (encryptionMethod.value === 'AES') {

    if (!encryptionKey.value.trim()) {

      errorMessage.value =
        'Please enter an encryption key.'

      return
    }


    try {

      const encrypted =
        CryptoJS.AES.encrypt(
          inputText.value,
          encryptionKey.value
        ).toString()

      result.value = encrypted

      await scrollToResult()

    } catch (error) {

      console.error(error)

      errorMessage.value =
        'AES encryption failed.'

    }

    return
  }


  // CAESAR
  if (encryptionMethod.value === 'Caesar') {

    result.value =
      caesarCipher(
        inputText.value,
        shiftValue.value
      )

    await scrollToResult()

  }

}


// DECRYPT
async function decryptText() {

  errorMessage.value = ''


  if (!result.value.trim()) {

    errorMessage.value =
      'Please encrypt some text first.'

    return
  }


  // AES
  if (encryptionMethod.value === 'AES') {

    if (!encryptionKey.value.trim()) {

      errorMessage.value =
        'Please enter an encryption key.'

      return
    }


    try {

      const decrypted =
        CryptoJS.AES.decrypt(
          result.value,
          encryptionKey.value
        )


      const originalText =
        decrypted.toString(
          CryptoJS.enc.Utf8
        )


      if (!originalText) {

        errorMessage.value =
          'Decryption failed. Please check your encryption key.'

        return
      }


      result.value = originalText

      await scrollToResult()

    } catch (error) {

      console.error(error)

      errorMessage.value =
        'AES decryption failed.'

    }

    return
  }


  // CAESAR
  if (encryptionMethod.value === 'Caesar') {

    result.value =
      caesarCipher(
        result.value,
        -shiftValue.value
      )

    await scrollToResult()

  }

}


// CAESAR CIPHER
function caesarCipher(
  text: string,
  shift: number
): string {

  const normalizedShift =
    ((shift % 26) + 26) % 26


  return text
    .split('')
    .map((character) => {

      const code =
        character.charCodeAt(0)


      // UPPERCASE
      if (
        code >= 65 &&
        code <= 90
      ) {

        return String.fromCharCode(
          ((code - 65 + normalizedShift) % 26) + 65
        )

      }


      // LOWERCASE
      if (
        code >= 97 &&
        code <= 122
      ) {

        return String.fromCharCode(
          ((code - 97 + normalizedShift) % 26) + 97
        )

      }


      // OTHER CHARACTERS
      return character

    })
    .join('')

}


// CLEAR
function clearAll() {

  inputText.value = ''
  encryptionKey.value = ''
  result.value = ''
  errorMessage.value = ''
  shiftValue.value = 3

}

</script>


<style scoped>

ion-content {

  --background: #fff5fa;
  --overflow: auto;

}


ion-content::part(scroll) {

  overflow-y: auto;
  -webkit-overflow-scrolling: touch;

}


.app-header ion-toolbar {

  --background: #ffb6d5;

  --color: #6f3b52;

}


.header-content {

  height: 68px;

  max-width: 900px;

  margin: auto;

  padding: 0 20px;

  display: flex;

  align-items: center;

  justify-content: space-between;

}


.brand {

  display: flex;

  align-items: center;

  gap: 10px;

}


.brand-icon {

  width: 42px;

  height: 42px;

  display: flex;

  align-items: center;

  justify-content: center;

  border-radius: 14px;

  background: #fff;

  box-shadow:
    0 4px 10px rgba(150, 75, 110, 0.12);

  font-size: 22px;

}


.brand-name {

  color: #6f3b52;

  font-size: 21px;

  font-weight: 800;

}


.brand-subtitle {

  color: #9c637a;

  font-size: 10px;

  font-weight: 700;

  letter-spacing: 1.5px;

}


.header-heart {

  color: #fff;

  font-size: 30px;

}


.page-container {

  width: 100%;

  max-width: 850px;

  min-height: 100%;

  margin: auto;

  padding: 30px 18px 40px;

  box-sizing: border-box;

  font-family: 'Fredoka', 'Trebuchet MS', sans-serif;

}


.page-container button,
.page-container input,
.page-container select,
.page-container textarea {

  font-family: inherit;

  box-sizing: border-box;

}


/* =========================
   HERO
========================= */

.hero-card {

  position: relative;

  overflow: hidden;

  display: flex;

  align-items: center;

  gap: 20px;

  margin-bottom: 25px;

  padding: 28px;

  border-radius: 25px;

  background: #ffd6e7;

  border: 3px solid #fff;

  box-shadow:
    0 8px 20px rgba(204, 116, 153, 0.12);

}


.hero-character {

  width: 90px;

  height: 90px;

  flex-shrink: 0;

  display: flex;

  align-items: center;

  justify-content: center;

  border-radius: 50%;

  background: #fff;

  border: 4px solid #ffabc9;

  font-size: 50px;

  box-shadow:
    0 5px 0 #ed91b3;

}


.hero-content {

  position: relative;

  z-index: 2;

  min-width: 0;

}


.small-title {

  font-family: 'Pacifico', cursive;

  color: #b45a7d;

  font-size: 20px;

  font-weight: 400;

  letter-spacing: 0;

  margin-bottom: 8px;

}


.hero-card h1 {

  margin: 0;

  color: #65354a;

  font-size: 30px;

  font-weight: 900;

}


.hero-card p {

  margin: 8px 0 0;

  max-width: 500px;

  color: #86546b;

  font-size: 14px;

  line-height: 1.5;

}


/* Decorative stars */

.sparkle {

  position: absolute;

  color: #f18ab2;

  font-size: 28px;

}


.sparkle-one {

  top: 10px;

  right: 30px;

}


.sparkle-two {

  bottom: 12px;

  right: 90px;

  font-size: 20px;

}


/* =========================
   MAIN CARD
========================= */

.main-card {

  padding: 28px;

  border-radius: 25px;

  background: #ffffff;

  border: 3px solid #ffd7e7;

  box-shadow:
    0 10px 30px rgba(170, 80, 120, 0.10);

  box-sizing: border-box;

}


/* =========================
   FORM SECTION
========================= */

.form-section {

  margin-bottom: 28px;

}


.section-heading {

  display: flex;

  gap: 12px;

  align-items: flex-start;

  margin-bottom: 12px;

}


.section-heading > div:last-child {

  min-width: 0;

}


.step-circle {

  width: 34px;

  height: 34px;

  flex-shrink: 0;

  display: flex;

  align-items: center;

  justify-content: center;

  border-radius: 50%;

  background: #ffc5dc;

  color: #7b3d57;

  font-weight: 800;

  font-size: 14px;

  box-shadow:
    0 3px 0 #ed9fbd;

}


.section-heading h2 {

  margin: 0;

  color: #65354a;

  font-size: 16px;

  font-weight: 800;

}


.section-heading p {

  margin: 3px 0 0;

  color: #a47789;

  font-size: 12px;

}


/* =========================
   INPUTS
========================= */

.cute-select,
.cute-input,
.cute-textarea,
.result-textarea {

  width: 100%;

  box-sizing: border-box;

  border: 2px solid #f4c9da;

  border-radius: 14px;

  background: #fff9fc;

  color: #65354a;

  font-size: 15px;

  transition: 0.2s;

}


.cute-select,
.cute-input {

  height: 48px;

  padding: 0 14px;

}


.cute-textarea,
.result-textarea {

  padding: 14px;

  resize: vertical;

  font-family: inherit;

  line-height: 1.5;

}


.cute-select:focus,
.cute-input:focus,
.cute-textarea:focus {

  outline: none;

  border-color: #f19abd;

  background: white;

  box-shadow:
    0 0 0 4px #ffe4ef;

}


/* =========================
   BUTTONS
========================= */

.button-container {

  display: grid;

  grid-template-columns: 1fr 1fr;

  gap: 14px;

  margin: 5px 0 20px;

}


.cute-button {

  height: 55px;

  border: none;

  border-radius: 16px;

  color: white;

  font-size: 15px;

  font-weight: 800;

  cursor: pointer;

  display: flex;

  align-items: center;

  justify-content: center;

  gap: 8px;

  box-shadow:
    0 5px 0 rgba(120, 50, 80, 0.20);

  transition: 0.15s;

}


.cute-button:active {

  transform: translateY(4px);

  box-shadow:
    0 1px 0 rgba(120, 50, 80, 0.20);

}


.encrypt-button {

  background: #ee86ae;

}


.decrypt-button {

  background: #c99adf;

}


.button-emoji {

  font-size: 19px;

}


/* =========================
   ERROR
========================= */

.error-box {

  display: flex;

  align-items: center;

  gap: 10px;

  margin-bottom: 20px;

  padding: 13px;

  border-radius: 13px;

  background: #fff0f3;

  border: 2px solid #ffc8d7;

  color: #a34663;

  font-size: 13px;

}


/* =========================
   RESULT
========================= */

.result-box {

  padding: 20px;

  border-radius: 18px;

  background: #fff4f9;

  border: 2px dashed #f2b8cf;

  box-sizing: border-box;

}


.result-title {

  display: flex;

  align-items: center;

  justify-content: space-between;

  margin-bottom: 12px;

}


.result-title h2 {

  margin: 0;

  color: #65354a;

  font-size: 17px;

  font-weight: 800;

}


.result-title p {

  margin: 3px 0 0;

  color: #a47789;

  font-size: 12px;

}


.heart-badge {

  width: 40px;

  height: 40px;

  display: flex;

  align-items: center;

  justify-content: center;

  border-radius: 50%;

  background: #ffd4e5;

  font-size: 20px;

}


.result-textarea {

  background: white;

}


/* =========================
   CLEAR BUTTON
========================= */

.clear-button {

  width: 100%;

  height: 45px;

  margin-top: 15px;

  border: 2px solid #f1c6d8;

  border-radius: 14px;

  background: white;

  color: #9b6278;

  font-size: 14px;

  font-weight: 700;

  cursor: pointer;

}


.clear-button:hover {

  background: #fff6fa;

}


/* =========================
   FOOTER
========================= */

.footer {

  display: flex;

  justify-content: center;

  align-items: center;

  gap: 8px;

  margin-top: 22px;

  color: #bd8ca0;

  font-size: 11px;

}


/* =========================
   MOBILE
========================= */

@media (max-width: 600px) {

  ion-content {
    --overflow: auto;
  }

  .page-container {
    width: 100%;
    max-width: 100%;
    min-height: 100%;
    padding: 16px 10px calc(30px + env(safe-area-inset-bottom));
    box-sizing: border-box;
  }

  .hero-card {
    width: 100%;
    box-sizing: border-box;
    flex-direction: column;
    text-align: center;
    padding: 22px 16px;
    margin-bottom: 18px;
  }

  .hero-card h1 {
    font-size: 24px;
  }

  .hero-card p {
    font-size: 13px;
  }

  .main-card {
    width: 100%;
    box-sizing: border-box;
    padding: 18px 14px;
  }

  .form-section {
    margin-bottom: 22px;
  }

  .section-heading {
    gap: 9px;
  }

  .section-heading h2 {
    font-size: 15px;
  }

  .section-heading p {
    font-size: 11px;
  }

  .button-container {
    grid-template-columns: 1fr;
    gap: 10px;
  }

  .cute-button {
    width: 100%;
    height: 50px;
  }

  .result-box {
    width: 100%;
    box-sizing: border-box;
    padding: 15px;
    margin-top: 5px;
  }

  .result-title {
    width: 100%;
  }

  .result-title h2 {
    font-size: 16px;
  }

  .result-title p {
    font-size: 11px;
  }

  .cute-select,
  .cute-input,
  .cute-textarea,
  .result-textarea {
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
    font-size: 16px;
  }

  .cute-textarea,
  .result-textarea {
    min-height: 120px;
  }

  .clear-button {
    width: 100%;
    margin-top: 12px;
  }

  .header-content {
    padding: 0 12px;
  }

  .brand-name {
    font-size: 18px;
  }

  .brand-subtitle {
    font-size: 9px;
  }

  .header-heart {
    font-size: 24px;
  }

}


/* TABLET */

@media (min-width: 601px) and (max-width: 900px) {

  .page-container {
    padding: 24px 20px 36px;
  }

  .hero-card,
  .main-card {
    padding: 24px;
  }

}


/* VERY SMALL PHONES */

@media (max-width: 360px) {

  .page-container {
    padding-right: 8px;
    padding-left: 8px;
  }

  .main-card {
    padding: 16px 12px;
  }

  .hero-card {
    padding: 20px 12px;
  }

  .hero-card h1 {
    font-size: 22px;
  }

}

</style>
```
