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
          Encrypt and decrypt your text using AES encryption.
        </p>

        <!-- Text Input -->
        <div class="input-group">
          <label>Enter Text</label>

          <textarea
            v-model="inputText"
            placeholder="Enter your text here..."
            rows="6"
          ></textarea>
        </div>

        <!-- Encryption Key -->
        <div class="input-group">
          <label>Encryption Key</label>

          <input
            v-model="encryptionKey"
            type="password"
            placeholder="Enter your encryption key"
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

const inputText = ref('')
const encryptionKey = ref('')
const result = ref('')
const errorMessage = ref('')


// ENCRYPT
function encryptText() {

  errorMessage.value = ''

  if (!inputText.value.trim()) {
    errorMessage.value = 'Please enter text to encrypt.'
    return
  }

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
    errorMessage.value = 'Encryption failed.'

  }
}


// DECRYPT
function decryptText() {

  errorMessage.value = ''

  if (!result.value.trim()) {
    errorMessage.value = 'Please encrypt some text first.'
    return
  }

  if (!encryptionKey.value.trim()) {
    errorMessage.value = 'Please enter the encryption key.'
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
      'Decryption failed. Please check the encrypted text and key.'

  }
}


// CLEAR
function clearAll() {

  inputText.value = ''
  encryptionKey.value = ''
  result.value = ''
  errorMessage.value = ''

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


/* Input sections */

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


/* Input */

input {
  width: 100%;
  box-sizing: border-box;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 16px;
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