<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>ProjSec</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <div class="container">

        <h1>Secure Text</h1>
        <p class="description">
          Encrypt and decrypt your text using AES or Caesar Cipher.
        </p>

        <!-- Encryption Method -->
        <div class="input-group">
          <label>Encryption Method</label>

          <select v-model="encryptionMethod">
            <option value="AES">AES</option>
            <option value="Caesar">Caesar Cipher</option>
          </select>
        </div>

        <!-- Text Input -->
        <div class="input-group">
          <label>Enter Text</label>

          <textarea
            v-model="inputText"
            placeholder="Enter your text here..."
            rows="6"
          ></textarea>
        </div>

        <!-- AES Key -->
        <div
          v-if="encryptionMethod === 'AES'"
          class="input-group"
        >
          <label>Encryption Key</label>

          <input
            v-model="encryptionKey"
            type="password"
            placeholder="Enter your encryption key"
          />
        </div>

        <!-- Caesar Shift -->
        <div
          v-if="encryptionMethod === 'Caesar'"
          class="input-group"
        >
          <label>Shift Value</label>

          <input
            v-model.number="shiftValue"
            type="number"
            placeholder="Enter shift value"
          />
        </div>

        <!-- Buttons -->
        <div class="buttons">

          <button
            class="encrypt-button"
            @click="encryptText"
          >
            🔒 Encrypt
          </button>

          <button
            class="decrypt-button"
            @click="decryptText"
          >
            🔓 Decrypt
          </button>

        </div>

        <!-- Error -->
        <p v-if="errorMessage" class="error">
          {{ errorMessage }}
        </p>

        <!-- Result -->
        <div class="input-group">
          <label>Result</label>

          <textarea
            v-model="result"
            rows="6"
            readonly
            placeholder="Your result will appear here..."
          ></textarea>
        </div>

        <!-- Clear -->
        <button
          class="clear-button"
          @click="clearAll"
        >
          Clear
        </button>

      </div>
    </ion-content>
  </ion-page>
</template>


<script setup lang="ts">

import { ref } from 'vue'
import CryptoJS from 'crypto-js'


// -------------------------
// VARIABLES
// -------------------------

const inputText = ref('')
const encryptionKey = ref('')
const result = ref('')
const errorMessage = ref('')

const encryptionMethod = ref('AES')
const shiftValue = ref(3)


// -------------------------
// ENCRYPT
// -------------------------

function encryptText() {

  errorMessage.value = ''

  if (!inputText.value.trim()) {
    errorMessage.value = 'Please enter text to encrypt.'
    return
  }


  // AES ENCRYPTION
  if (encryptionMethod.value === 'AES') {

    if (!encryptionKey.value.trim()) {
      errorMessage.value = 'Please enter an encryption key.'
      return
    }

    try {

      const encrypted = CryptoJS.AES.encrypt(
        inputText.value,
        encryptionKey.value
      ).toString()

      result.value = encrypted

    } catch (error) {

      console.error(error)
      errorMessage.value = 'AES encryption failed.'

    }

    return
  }


  // CAESAR CIPHER
  if (encryptionMethod.value === 'Caesar') {

    result.value = caesarCipher(
      inputText.value,
      shiftValue.value
    )

  }
}


// -------------------------
// DECRYPT
// -------------------------

function decryptText() {

  errorMessage.value = ''


  if (!result.value.trim()) {
    errorMessage.value = 'Please encrypt some text first.'
    return
  }


  // AES DECRYPTION
  if (encryptionMethod.value === 'AES') {

    if (!encryptionKey.value.trim()) {
      errorMessage.value = 'Please enter an encryption key.'
      return
    }

    try {

      const decrypted = CryptoJS.AES.decrypt(
        result.value,
        encryptionKey.value
      )

      const originalText = decrypted.toString(
        CryptoJS.enc.Utf8
      )


      if (!originalText) {

        errorMessage.value =
          'Decryption failed. Please check your encryption key.'

        return
      }

      result.value = originalText

    } catch (error) {

      console.error(error)

      errorMessage.value =
        'AES decryption failed.'

    }

    return
  }


  // CAESAR CIPHER DECRYPTION
  if (encryptionMethod.value === 'Caesar') {

    result.value = caesarCipher(
      result.value,
      -shiftValue.value
    )

  }
}


// -------------------------
// CAESAR CIPHER
// -------------------------

function caesarCipher(
  text: string,
  shift: number
): string {

  const normalizedShift =
    ((shift % 26) + 26) % 26

  return text
    .split('')
    .map((character) => {

      const code = character.charCodeAt(0)


      // Uppercase letters
      if (code >= 65 && code <= 90) {

        return String.fromCharCode(
          ((code - 65 + normalizedShift) % 26) + 65
        )

      }


      // Lowercase letters
      if (code >= 97 && code <= 122) {

        return String.fromCharCode(
          ((code - 97 + normalizedShift) % 26) + 97
        )

      }


      // Spaces, numbers, punctuation
      return character

    })
    .join('')
}


// -------------------------
// CLEAR
// -------------------------

function clearAll() {

  inputText.value = ''
  encryptionKey.value = ''
  result.value = ''
  errorMessage.value = ''
  shiftValue.value = 3

}

</script>


<style scoped>

.container {
  max-width: 700px;
  margin: 0 auto;
}


h1 {
  margin-top: 20px;
  margin-bottom: 5px;
  font-size: 28px;
  font-weight: bold;
}


.description {
  margin-bottom: 25px;
  color: #666;
}


/* Input groups */

.input-group {
  margin-bottom: 20px;
}


.input-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
}


/* Textarea */

textarea {
  width: 100%;
  box-sizing: border-box;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 16px;
  font-family: Arial, sans-serif;
  resize: vertical;
}


/* Text input */

input,
select {
  width: 100%;
  box-sizing: border-box;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 16px;
  background: white;
}


/* Buttons */

.buttons {
  display: flex;
  gap: 12px;
  margin-top: 25px;
  margin-bottom: 15px;
}


.buttons button {
  flex: 1;
  padding: 12px;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
}


.encrypt-button {
  background: #3880ff;
  color: white;
}


.decrypt-button {
  background: #2dd36f;
  color: white;
}


/* Clear button */

.clear-button {
  width: 100%;
  padding: 12px;
  margin-top: 10px;
  border: none;
  border-radius: 8px;
  background: #92949c;
  color: white;
  font-size: 16px;
  cursor: pointer;
}


/* Error */

.error {
  margin: 10px 0;
  padding: 10px;
  border-radius: 6px;
  background: #ffe5e5;
  color: #d00000;
}

</style>