// encrypt.js
// Usage: node encrypt.js <input-file> <password>
// Produces JSON { iv, salt, ciphertext } to stdout

const fs = require('fs');
const CryptoJS = require('crypto-js');

if (process.argv.length < 4) {
  console.error('Usage: node encrypt.js <input-file> <password>');
  process.exit(2);
}

const file = process.argv[2];
const pass = process.argv[3];
const plaintext = fs.readFileSync(file, 'utf8');

// Use CryptoJS PBKDF2 + AES (CBC) via CryptoJS
const salt = CryptoJS.lib.WordArray.random(128/8).toString();
const key = CryptoJS.PBKDF2(pass, CryptoJS.enc.Hex.parse(salt), { keySize: 256/32, iterations: 10000 });
const iv = CryptoJS.lib.WordArray.random(128/8);
const encrypted = CryptoJS.AES.encrypt(plaintext, key, { iv: iv });

const out = {
  salt: salt,
  iv: iv.toString(CryptoJS.enc.Hex),
  ciphertext: encrypted.ciphertext.toString(CryptoJS.enc.Base64)
};
console.log(JSON.stringify(out));
