# Pay10 Payment Gateway Frontend

A modern web interface for testing Pay10 payment gateway integration and decrypting encrypted response data.

## Features

- **Payment Request Generator**: Create and submit payment requests to Pay10
- **Hash Generation**: Automatic SHA-256 hash generation for payment security
- **Response Decryption**: Client-side AES-256-CBC decryption of Pay10 responses
- **Response Parser**: Automatic parsing of `KEY=VALUE~KEY=VALUE` format
- **Modern UI**: Beautiful, responsive design with real-time feedback
- **Cross-Origin Support**: Handles CORS issues with multiple submission methods

## Live Demo

Open `payment_gateway.html` in your browser - no server required!

## Files

- `payment_gateway.html` - Complete payment gateway interface (standalone)

## Usage

### 1. Payment Request Generation

1. **Open the HTML file** in your browser
2. **Fill in payment details**:
   - Order ID (auto-generated)
   - Amount
   - Transaction type (SALE, AUTH, etc.)
   - Currency code
   - Return URL
   - Pay ID
   - Salt (for hash generation)

3. **Submit payment**:
   - ✅ **New Tab** (recommended): Bypasses CORS, opens Pay10 in new tab
   - 🔄 **Iframe** (advanced): Displays response in embedded frame

### 2. Response Decryption

The interface includes a built-in decryption tool:

1. **Paste encrypted data** (Base64 format)
2. **Verify key and IV** (pre-configured for Pay10)
3. **Click "Decrypt Response"**
4. **View parsed results** in JSON format

## Pay10 Configuration

### Default Parameters

```javascript
// Payment Gateway
const payId = "5011767734260001";
const salt = "f05666e27b65421d";
const returnUrl = "http://localhost:5000/callback";

// Decryption (matches Node.js crypto)
const secretKey = "E9DC485723454ACC93066392F735FC58"; // 32 UTF-8 chars
const iv = "E9DC485723454ACC";                       // 16 UTF-8 chars
```

### Hash Generation

The interface automatically generates SHA-256 hashes:

```javascript
// Parameters are sorted alphabetically and concatenated
const sortedKeys = Object.keys(params).sort();
const concatenated = sortedKeys.map(k => `${k}=${params[k]}`).join("~");
const stringToHash = concatenated + salt;
const hash = SHA256(stringToHash).toUpperCase();
```

## Decryption Details

### Algorithm: AES-256-CBC

The frontend uses the same decryption method as your Node.js backend:

```javascript
// Equivalent to Node.js:
// crypto.createDecipheriv("aes-256-cbc", 
//     Buffer.from(secretKey, "utf8"), 
//     Buffer.from(iv, "utf8"))

const key = CryptoJS.enc.Utf8.parse(secretKey);
const ivBytes = CryptoJS.enc.Utf8.parse(iv);
const decrypted = CryptoJS.AES.decrypt(encryptedData, key, {
    iv: ivBytes,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7
});
```

### Parameters

- **Algorithm**: AES-256-CBC
- **Key**: `E9DC485723454ACC93066392F735FC58` (32 UTF-8 chars = 256 bits)
- **IV**: `E9DC485723454ACC` (16 UTF-8 chars = 128 bits)
- **Padding**: PKCS7
- **Input**: Base64 encoded encrypted data
- **Output**: UTF-8 plain text

## Response Format

Pay10 responses are automatically parsed from:
```
TXNTYPE=SALE~AMOUNT=100~STATUS=Captured~ORDERID=ORDER123
```

To JSON:
```json
{
  "TXNTYPE": "SALE",
  "AMOUNT": "100",
  "STATUS": "Captured",
  "ORDERID": "ORDER123"
}
```

## Integration Methods

### Method 1: New Tab Submission (Recommended)

- ✅ Bypasses CORS restrictions
- ✅ Works with all browsers
- ✅ User can complete payment in Pay10's interface
- ✅ Redirects back to your callback URL

### Method 2: Iframe Submission (Advanced)

- ⚠️ May face CORS restrictions
- 🔄 Displays Pay10 interface embedded in your page
- 🧪 Good for testing and development

## Testing

### Built-in Test Features

1. **🧪 Test Button**: Validates encryption/decryption round-trip
2. **Auto-generated Order IDs**: Unique order IDs for each test
3. **Real-time Hash Generation**: Updates hash as you type
4. **Pre-filled Data**: Includes working test data

### Test Workflow

1. Click "🧪 Test with Known Working Parameters"
2. Verify encryption/decryption works
3. Submit a real payment request
4. Use the decryption tool for responses

## Customization

### Update Pay10 Credentials

```javascript
// Update these values in the HTML file:
document.getElementById('payId').value = 'YOUR_PAY_ID';
document.getElementById('salt').value = 'YOUR_SALT';
document.getElementById('returnUrl').value = 'YOUR_CALLBACK_URL';
```

### Styling

The interface uses modern CSS with:
- Gradient backgrounds
- Smooth animations
- Responsive design
- Beautiful form controls
- Real-time feedback

### Add Custom Fields

```html
<div class="form-group">
    <label for="customField">Custom Field</label>
    <input type="text" id="customField" name="CUSTOM_FIELD" value="">
</div>
```

## Browser Compatibility

- ✅ Chrome 60+
- ✅ Firefox 55+
- ✅ Safari 12+
- ✅ Edge 79+
- ✅ Mobile browsers

## Dependencies

### External Libraries (CDN)

- **CryptoJS 4.1.1**: For hash generation and AES decryption
  ```html
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
  ```

### No Build Process Required

This is a standalone HTML file with no build dependencies:
- No Node.js required
- No webpack/bundler needed
- No package.json
- Just open in browser!

## Security Notes

### Client-Side Decryption

- 🔐 Decryption happens in the browser
- ⚠️ Secret keys are visible in source code
- 🧪 Suitable for testing and development
- 🏭 For production, use server-side decryption

### Recommendations

1. **Development**: Use this frontend for testing
2. **Production**: Move decryption to your backend
3. **Keys**: Store sensitive keys server-side
4. **HTTPS**: Always use HTTPS in production

## Troubleshooting

### Common Issues

1. **Hash mismatch**: Verify all parameters are included and sorted
2. **Decryption fails**: Check key, IV, and data format
3. **CORS errors**: Use "New Tab" submission method
4. **Missing data**: Ensure all required fields are filled

### Debug Mode

Open browser console to see detailed logs:
```
🔓 Starting decryption with exact Node.js parameters...
Secret Key (UTF-8): E9DC485723454ACC93066392F735FC58 - Length: 32 chars
IV (UTF-8): E9DC485723454ACC - Length: 16 chars
✅ Decryption successful!
```

## Integration with Backend

This frontend works perfectly with the Pay10 Callback Server:

1. **Frontend**: Generates payment requests and decrypts responses
2. **Backend**: Handles callbacks and server-side processing
3. **Pay10**: Processes payments and sends encrypted responses

## Contributing

1. Fork the repository
2. Make your changes to `payment_gateway.html`
3. Test in multiple browsers
4. Submit a pull request

## License

MIT License - see LICENSE file for details

## Support

For Pay10-specific issues, consult the Pay10 documentation or contact their support team. 