# Session Recording SDK

A privacy-conscious session recording SDK for hosted session tracking service. This SDK captures user interactions, console logs, and network requests while respecting privacy settings. Simply include the SDK and start recording - all data is automatically sent to our secure backend at `api.sessions.chxse.dev`.

## Quick Start

### 1. Include the SDK

Add the SDK script to your HTML:

```html
<script src="https://chaseleto.github.io/public-sdk/index.umd.js"></script>
```

### 2. Initialize the SDK

```javascript
// Initialize with your API key
SessionSDK.init({
  apiKey: 'your-api-key',
  uploadInterval: 30000, // 30 seconds
});
```

### 3. Start Recording

```javascript
// Start recording user interactions
SessionSDK.startRecording();
```

## Configuration Options

### Basic Configuration

```javascript
SessionSDK.init({
  // Required: Your API key for authentication
  apiKey: 'your-api-key',
  
  // Optional: Upload interval in milliseconds (default: 30000)
  uploadInterval: 30000,
  
  // Optional: Custom headers to send with requests
  headers: {
    'X-Custom-Header': 'value'
  },
  
  // Optional: Maximum retry attempts for failed uploads (default: 3)
  maxRetries: 3,
  
  // Optional: Delay between retry attempts in milliseconds (default: 1000)
  retryDelay: 1000,
  
  // Optional: User ID for session attribution
  userId: 'user-123',
  
  // Optional: Custom attributes to attach to sessions
  attributes: {
    page: 'checkout',
    userType: 'premium'
  }
});
```

### Advanced Configuration

```javascript
SessionSDK.init({
  apiKey: 'your-api-key',
  
  // rrweb recording options
  recordOptions: {
    // Capture mouse movements (default: true)
    mousemove: true,
    
    // Capture mouse interactions (default: true)
    mouseInteraction: true,
    
    // Capture scroll events (default: true)
    scroll: true,
    
    // Capture input events (default: true)
    input: true,
    
    // Capture viewport resize events (default: true)
    viewportResize: true,
    
    // Capture canvas mutations (default: false)
    canvas: false,
    
    // Capture console logs (default: true)
    console: true,
    
    // Capture network requests (default: true)
    network: true,
    
    // Sampling rate for mouse movements (0-1, default: 1)
    mousemoveSampling: 1,
    
    // Block certain elements from recording
    blockClass: 'no-record',
    blockSelector: '[data-no-record]',
    
    // Mask sensitive input fields
    maskInputOptions: {
      password: true,
      email: true,
      tel: true
    }
  }
});
```

## API Reference

### Core Methods

#### `SessionSDK.init(config)`
Initializes the SDK with the provided configuration.

#### `SessionSDK.startRecording()`
Starts recording user interactions. The SDK will automatically:
- Capture DOM changes
- Record mouse movements and clicks
- Monitor console logs
- Track network requests
- Upload data periodically

#### `SessionSDK.stop()`
Stops recording and uploads any remaining data.

#### `SessionSDK.destroy()`
Completely destroys the SDK instance and cleans up resources.

### Session Management

#### `SessionSDK.setUserId(userId)`
Sets or updates the user ID for the current session.

```javascript
SessionSDK.setUserId('user-123');
```

#### `SessionSDK.setAttributes(attributes)`
Sets or updates custom attributes for the session.

```javascript
SessionSDK.setAttributes({
  page: 'checkout',
  userType: 'premium',
  campaign: 'summer-sale'
});
```

#### `SessionSDK.getSessionId()`
Returns the current session ID.

```javascript
const sessionId = SessionSDK.getSessionId();
console.log('Current session:', sessionId);
```

#### `SessionSDK.getSessionDuration()`
Returns the current session duration in milliseconds.

```javascript
const duration = SessionSDK.getSessionDuration();
console.log('Session duration:', duration, 'ms');
```

#### `SessionSDK.isActive()`
Returns whether recording is currently active.

```javascript
if (SessionSDK.isActive()) {
  console.log('Recording is active');
}
```

### Utility Functions

#### `SessionSDK.getInstance()`
Returns the current SDK instance for advanced usage.

```javascript
const sdk = SessionSDK.getInstance();
// Access internal methods if needed
```

## Privacy Features

### Do Not Track Support
The SDK automatically respects the browser's Do Not Track setting. If DNT is enabled, recording will not start.

### Input Masking
Sensitive input fields can be automatically masked:

```javascript
SessionSDK.init({
  apiKey: 'your-api-key',
  recordOptions: {
    maskInputOptions: {
      password: true,    // Mask password fields
      email: true,       // Mask email fields
      tel: true,         // Mask phone fields
      text: false        // Don't mask regular text fields
    }
  }
});
```

### Element Blocking
Block specific elements from being recorded:

```html
<!-- Block by class -->
<div class="no-record">This won't be recorded</div>

<!-- Block by data attribute -->
<div data-no-record>This won't be recorded either</div>
```

```javascript
SessionSDK.init({
  apiKey: 'your-api-key',
  recordOptions: {
    blockClass: 'no-record',
    blockSelector: '[data-no-record]'
  }
});
```

## Error Handling

The SDK includes built-in error handling and retry logic:

```javascript
// Failed uploads are automatically retried
SessionSDK.init({
  apiKey: 'your-api-key',
  maxRetries: 5,        // Retry up to 5 times
  retryDelay: 2000      // Wait 2 seconds between retries
});
```

## Complete Example

```html
<!DOCTYPE html>
<html>
<head>
    <title>Session Recording Example</title>
</head>
<body>
    <h1>Welcome to our site</h1>
    
    <!-- This form will be recorded -->
    <form id="contact-form">
        <input type="text" name="name" placeholder="Your name">
        <input type="email" name="email" placeholder="Your email">
        <input type="password" name="password" placeholder="Password">
        <button type="submit">Submit</button>
    </form>
    
    <!-- This section won't be recorded -->
    <div class="no-record">
        <p>Sensitive information here</p>
    </div>
    
    <script src="https://chaseleto.github.io/public-sdk/index.umd.js"></script>
    <script>
        // Initialize the SDK
        SessionSDK.init({
            apiKey: 'your-api-key',
            uploadInterval: 30000,
            userId: 'user-123',
            attributes: {
                page: 'contact',
                source: 'organic'
            },
            recordOptions: {
                maskInputOptions: {
                    password: true,
                    email: true
                },
                blockClass: 'no-record'
            }
        });
        
        // Start recording
        SessionSDK.startRecording();
        
        // Example: Update attributes when user performs actions
        document.getElementById('contact-form').addEventListener('submit', function() {
            SessionSDK.setAttributes({
                ...SessionSDK.getAttributes(),
                formSubmitted: true
            });
        });
    </script>
</body>
</html>
```

## Data Format

The SDK automatically sends session data to our secure backend in the following format:

```javascript
{
  "sessionId": "unique-session-id",
  "userId": "user-123",
  "attributes": {
    "page": "contact",
    "source": "organic"
  },
  "metadata": {
    "userAgent": "Mozilla/5.0...",
    "url": "https://example.com/contact",
    "referrer": "https://google.com",
    "viewport": "1920x1080",
    "timestamp": 1640995200000,
    "timezone": "America/New_York",
    "language": "en-US"
  },
  "events": [...], // rrweb events
  "consoleLogs": [...], // console log entries
  "networkRequests": [...], // network request logs
  "timestamp": 1640995200000,
  "duration": 45000
}
```

## Browser Support

- Chrome 60+
- Firefox 55+
- Safari 12+
- Edge 79+

## License

This SDK is provided as part of our hosted session recording service. Please ensure compliance with privacy laws and regulations in your jurisdiction.

## Support

For issues or questions, please refer to the main project documentation or create an issue in the repository.

 