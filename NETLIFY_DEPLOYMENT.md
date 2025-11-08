# Netlify Deployment Guide for CinePro Backend

This guide will help you deploy your CinePro backend API to Netlify.

## Prerequisites

1. A Netlify account (sign up at https://www.netlify.com/)
2. Your project pushed to a Git repository (GitHub, GitLab, or Bitbucket)
3. TMDB API key from https://www.themoviedb.org/settings/api

## Deployment Steps

### Option 1: Deploy via Netlify Dashboard (Recommended)

1. **Log in to Netlify**
   - Go to https://app.netlify.com/
   - Sign in with your account

2. **Import Your Repository**
   - Click "Add new site" → "Import an existing project"
   - Choose your Git provider (GitHub/GitLab/Bitbucket)
   - Authorize Netlify to access your repositories
   - Select your `backend` repository

3. **Configure Build Settings**
   - Build command: `npm install`
   - Publish directory: `.`
   - Functions directory: `netlify/functions`
   
   These settings are already configured in `netlify.toml`, so they should auto-populate.

4. **Set Environment Variables**
   Before deploying, add these environment variables:
   - Go to "Site settings" → "Environment variables"
   - Add the following variables:
     - `TMDB_API_KEY`: Your TMDB API key
     - `PORT`: `8888` (Netlify uses port 8888 for functions)
     - `ALLOWED_ORIGINS`: `["*"]` or specify your frontend URLs as a JSON array
   
   Example:
   ```
   TMDB_API_KEY=your_actual_tmdb_api_key_here
   PORT=8888
   ALLOWED_ORIGINS=["https://yourfrontend.netlify.app","https://yourdomain.com"]
   ```

5. **Deploy**
   - Click "Deploy site"
   - Wait for the build to complete (first build may take 2-3 minutes)
   - Once deployed, you'll get a URL like: `https://your-site-name.netlify.app`

### Option 2: Deploy via Netlify CLI

1. **Install Netlify CLI**
   ```bash
   npm install -g netlify-cli
   ```

2. **Login to Netlify**
   ```bash
   netlify login
   ```

3. **Install Dependencies**
   ```bash
   npm install
   ```

4. **Initialize Netlify Site**
   ```bash
   netlify init
   ```
   - Follow the prompts to create a new site or link to existing one
   - Build command: `npm install`
   - Functions directory: `netlify/functions`

5. **Set Environment Variables**
   ```bash
   netlify env:set TMDB_API_KEY "your_actual_tmdb_api_key_here"
   netlify env:set PORT "8888"
   netlify env:set ALLOWED_ORIGINS '["*"]'
   ```

6. **Deploy**
   ```bash
   netlify deploy --prod
   ```

## Testing Your Deployment

Once deployed, test your API endpoints:

```bash
# Test home endpoint
curl https://your-site-name.netlify.app/

# Test movie endpoint (replace TMDB_ID with actual ID)
curl https://your-site-name.netlify.app/movie/550

# Test TV show endpoint
curl "https://your-site-name.netlify.app/tv/1399?s=1&e=1"

# Test cache stats
curl https://your-site-name.netlify.app/cache-stats
```

## Important Notes

### Serverless Architecture
- Netlify uses serverless functions, which means your Express app runs on-demand
- Cold starts may add 1-2 seconds to first request after inactivity
- Functions have a 10-second execution timeout on free tier, 26 seconds on Pro

### Environment Variables
- Never commit your `.env` file to Git
- Always set environment variables through Netlify dashboard or CLI
- Update `ALLOWED_ORIGINS` to include your actual frontend URL(s)

### Custom Domain (Optional)
- Go to "Domain settings" in Netlify dashboard
- Add your custom domain
- Update DNS records as instructed by Netlify

### Continuous Deployment
- Once set up, any push to your main branch automatically triggers a new deployment
- You can configure deploy contexts (production, branch deploys, preview deploys)

## Troubleshooting

### Build Fails
- Check build logs in Netlify dashboard
- Ensure all dependencies are in `package.json`
- Verify Node.js version compatibility

### Function Errors
- Check function logs in Netlify dashboard
- Verify environment variables are set correctly
- Test locally first: `netlify dev`

### CORS Issues
- Update `ALLOWED_ORIGINS` environment variable with your frontend URL
- Ensure it's a valid JSON array format

### API Not Responding
- Check if functions are deployed: go to "Functions" tab in dashboard
- Verify the function name is `api`
- Check function logs for errors

## Local Testing with Netlify Dev

Test your deployment locally before pushing:

```bash
# Install dependencies
npm install

# Set up local environment variables
cp .env_example .env
# Edit .env with your actual values

# Start Netlify dev server
netlify dev
```

This will simulate the Netlify environment locally at `http://localhost:8888`

## File Structure

```
backend-1.0.2/
├── netlify/
│   └── functions/
│       └── api.js          # Serverless function wrapper
├── netlify.toml            # Netlify configuration
├── index.js                # Main Express app
├── package.json            # Dependencies (includes serverless-http)
└── src/                    # Your application code
```

## Next Steps

1. Install the new dependency:
   ```bash
   npm install
   ```

2. Push changes to your Git repository:
   ```bash
   git add .
   git commit -m "Add Netlify deployment configuration"
   git push origin main
   ```

3. Follow one of the deployment options above

4. Update your frontend to use the new Netlify URL

## Support

- Netlify Documentation: https://docs.netlify.com/
- CinePro Documentation: https://cinepro.mintlify.app/
- Report issues: https://github.com/cinepro-org/backend/issues
