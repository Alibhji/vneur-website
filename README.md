# VNour Website

A Vue.js website featuring 3D text "VNour" rendered with Three.js, including an email contact link.

## Features

- 🎨 3D animated text "VNour" using Three.js
- 📧 Email contact link
- 🐳 Docker containerization
- ☁️ Cloudflare Pages ready

## Local Development

### Prerequisites

- Node.js 20+ 
- npm or yarn

### Setup

1. Install dependencies:
```bash
npm install
```

2. Start development server:
```bash
npm run dev
```

3. Open your browser at `http://localhost:5173`

## Docker

### Build and Run with Docker

1. Build the Docker image:
```bash
docker build -t vneur-website .
```

2. Run the container:
```bash
docker run -p 8080:80 vneur-website
```

Or use docker compose:
```bash
docker compose up -d
```

3. Open your browser at `http://localhost:8080`

## Cloudflare Pages Deployment

This project is configured for Cloudflare Pages deployment:

1. **Build Command**: `npm run build`
2. **Build Output Directory**: `dist`
3. **Node Version**: 20

The following files are included for Cloudflare Pages:
- `_headers` - Security headers
- `_redirects` - SPA routing support

### Cloudflare Pages Setup

1. Connect your GitHub repository to Cloudflare Pages
2. Set the build command: `npm run build`
3. Set the build output directory: `dist`
4. Deploy!

## Project Structure

```
vneur-website/
├── src/
│   ├── components/
│   │   ├── ThreeJSText.vue    # 3D text component
│   │   └── EmailLink.vue      # Email contact link
│   ├── App.vue                # Main app component
│   └── main.js               # Entry point
├── Dockerfile                 # Docker configuration
├── docker-compose.yml         # Docker Compose config
├── nginx.conf                # Nginx configuration
├── vite.config.js            # Vite configuration
└── package.json              # Dependencies

```

## Customization

### Change Email Address

Edit `src/components/EmailLink.vue` and update the `email` data property:

```javascript
data() {
  return {
    email: 'your-email@example.com'
  }
}
```

### Modify 3D Text

Edit `src/components/ThreeJSText.vue` to change:
- Text content (currently "VNour")
- Colors
- Animation speed
- Text size and bevel

## Technologies

- Vue.js 3
- Three.js
- Vite
- Docker
- Nginx
