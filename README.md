# Spotter Website

This folder contains the website files for Spotter's deep linking and web presence.

## Files

- `index.html` - Main landing page
- `airport/index.html` - Airport detail page (handles `/airport/:icao` URLs)
- `spot/index.html` - Spotting location page (handles `/spot/:id` URLs)
- `apple-app-site-association` - Universal links configuration (root level)
- `.well-known/apple-app-site-association` - Universal links configuration (well-known path)

## Setup Instructions

### 1. Update Apple App Site Association

Edit both `apple-app-site-association` files and replace:
- `TEAMID` with your Apple Developer Team ID
- `com.yourcompany.Spotter` with your actual Bundle ID

Example:
```json
"appID": "ABC123XYZ.com.example.Spotter"
```

### 2. Deploy to Your Domain

Upload all files to your web server at `spotter.app`:

```
spotter.app/
├── index.html
├── apple-app-site-association
├── .well-known/
│   └── apple-app-site-association
├── airport/
│   └── index.html
└── spot/
    └── index.html
```

### 3. Server Configuration

**Important:** The `apple-app-site-association` files must be served with:
- Content-Type: `application/json`
- No file extension
- HTTPS only

**Nginx example:**
```nginx
location /apple-app-site-association {
    default_type application/json;
}

location /.well-known/apple-app-site-association {
    default_type application/json;
}
```

**Apache example (.htaccess):**
```apache
<Files "apple-app-site-association">
    ForceType application/json
</Files>
```

### 4. URL Routing

Configure your server to route dynamic URLs:

- `/airport/:icao` → `/airport/index.html`
- `/spot/:id` → `/spot/index.html`

**Nginx example:**
```nginx
location /airport/ {
    try_files $uri /airport/index.html;
}

location /spot/ {
    try_files $uri /spot/index.html;
}
```

**Vercel/Netlify (vercel.json or netlify.toml):**
```json
{
  "rewrites": [
    { "source": "/airport/:icao", "destination": "/airport/index.html" },
    { "source": "/spot/:id", "destination": "/spot/index.html" }
  ]
}
```

### 5. Add Open Graph Image

Create an `og-image.png` (1200x630px) with your app branding and upload it to the root:
```
spotter.app/og-image.png
```

This image will appear when links are shared on social media and iMessage.

## Testing Universal Links

1. Deploy the website with the updated `apple-app-site-association` file
2. Wait 15-30 minutes for Apple's CDN to cache the file
3. Test by:
   - Sending a link via iMessage (should show rich preview)
   - Tapping the link (should open the app if installed)
   - Long-press the link to see "Open in Spotter" option

## Verify Universal Links

Check if Apple can read your association file:
```
https://app-site-association.cdn-apple.com/a/v1/spotter.app
```

Or use Apple's validator:
```
https://search.developer.apple.com/appsearch-validation-tool/
```

## Features

- **Universal Links**: Links open directly in the app when installed
- **Rich Previews**: iMessage and social media show preview cards
- **Fallback**: Links work as regular web pages if app isn't installed
- **Auto-redirect**: iOS devices automatically redirect to the app after 1 second
- **SEO Friendly**: Proper meta tags for search engines and social sharing

## Customization

- Update colors in the CSS to match your brand
- Replace placeholder text and descriptions
- Add Firebase API calls to fetch real airport/spot data
- Customize the App Store link in `index.html`
