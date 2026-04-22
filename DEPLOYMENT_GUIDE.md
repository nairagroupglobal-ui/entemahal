# Masjid ERP - Deployment Guide

## Deployment Overview

This guide covers deploying Masjid ERP to production environments. Multiple deployment options are available depending on your infrastructure preferences.

---

## 🎯 Pre-Deployment Checklist

Before deploying to production, ensure:

- [ ] `.env.local` configured with production values
- [ ] MongoDB Atlas cluster set up with proper IP whitelist
- [ ] Stripe API keys configured (if using payments)
- [ ] All environment variables are secure
- [ ] Database backups configured
- [ ] SSL certificates obtained (for HTTPS)
- [ ] CORS configuration updated
- [ ] Rate limiting configured
- [ ] Logging and monitoring set up
- [ ] Security audit completed

---

## 📦 Build for Production

### 1. Local Build Test
```bash
cd entemahal
npm run build
npm start
```

Visit `http://localhost:3000/dashboard` to test the production build.

### Build Output
```
✓ Compiled successfully (9.0s with Turbopack)
○ Next.js app size: ~500KB (optimized)
○ Server size: ~50MB
○ Ready for deployment
```

---

## 🚀 Deployment Options

### Option 1: Vercel Deployment (Recommended)

**Best for**: Easiest setup, automatic deployments, built for Next.js

#### Steps:
1. **Push code to GitHub**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git push -u origin main
   ```

2. **Connect to Vercel**
   - Go to [vercel.com](https://vercel.com)
   - Click "New Project"
   - Import your GitHub repository
   - Select Next.js as framework

3. **Configure Environment**
   - Add environment variables in Vercel dashboard
   - Production variables (MongoDB URI, Stripe keys, etc.)

4. **Deploy**
   - Vercel automatically deploys on push to main
   - View deployment at `https://your-project.vercel.app`

#### Advantages:
- ✅ Zero-config deployment
- ✅ Automatic HTTPS
- ✅ CDN included
- ✅ Automatic scaling
- ✅ Built-in analytics
- ✅ Free tier available

#### Cost: Free for open source, $20+/month for production

---

### Option 2: AWS EC2 Deployment

**Best for**: Maximum control, custom infrastructure, higher traffic

#### Prerequisites:
- AWS account
- EC2 instance (t3.medium or larger)
- Ubuntu 22.04 LTS or Amazon Linux 2

#### Steps:

1. **Connect to EC2 Instance**
   ```bash
   ssh -i your-key.pem ec2-user@your-instance-ip
   ```

2. **Install Dependencies**
   ```bash
   sudo apt update
   sudo apt install -y nodejs npm git nginx
   ```

3. **Clone Repository**
   ```bash
   git clone https://github.com/your-repo/entemahal.git
   cd entemahal
   ```

4. **Install Project Dependencies**
   ```bash
   npm install
   npm run build
   ```

5. **Configure Environment**
   ```bash
   nano .env.local
   # Add production environment variables
   ```

6. **Create PM2 Configuration** (`ecosystem.config.js`)
   ```javascript
   module.exports = {
     apps: [{
       name: 'masjid-erp',
       script: 'npm',
       args: 'start',
       env: {
         NODE_ENV: 'production'
       }
     }]
   };
   ```

7. **Install PM2**
   ```bash
   sudo npm install -g pm2
   pm2 start ecosystem.config.js
   pm2 save
   ```

8. **Configure Nginx**
   ```nginx
   server {
     listen 80;
     server_name your-domain.com;
     
     location / {
       proxy_pass http://localhost:3000;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
     }
   }
   ```

9. **Enable HTTPS with Let's Encrypt**
   ```bash
   sudo apt install -y certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com
   ```

10. **Restart Services**
    ```bash
    sudo systemctl restart nginx
    pm2 restart all
    ```

#### Advantages:
- ✅ Full control
- ✅ Custom configurations
- ✅ Scalable
- ✅ Multiple instance support

#### Cost: ~$20-100/month (depending on instance type)

---

### Option 3: Docker Container Deployment

**Best for**: Consistency across environments, container orchestration

#### Steps:

1. **Create Dockerfile**
   ```dockerfile
   FROM node:20-alpine
   
   WORKDIR /app
   
   COPY package*.json ./
   RUN npm install --production
   
   COPY . .
   RUN npm run build
   
   ENV NODE_ENV=production
   EXPOSE 3000
   
   CMD ["npm", "start"]
   ```

2. **Create .dockerignore**
   ```
   node_modules
   .next
   .git
   .gitignore
   README.md
   *.log
   .env
   .env.*.local
   ```

3. **Build Docker Image**
   ```bash
   docker build -t masjid-erp:latest .
   ```

4. **Run Container Locally**
   ```bash
   docker run -p 3000:3000 \
     -e MONGODB_URI=mongodb://... \
     -e NEXT_PUBLIC_API_URL=http://localhost:3000 \
     masjid-erp:latest
   ```

5. **Push to Docker Hub**
   ```bash
   docker tag masjid-erp:latest your-username/masjid-erp:latest
   docker push your-username/masjid-erp:latest
   ```

6. **Deploy to Cloud (e.g., AWS ECS, Google Cloud Run)**
   - Configure container registry
   - Set environment variables
   - Deploy container

#### Advantages:
- ✅ Consistent environments
- ✅ Easy scaling
- ✅ Container orchestration support
- ✅ Multi-cloud support

---

### Option 4: Railway.app Deployment

**Best for**: Simple deployment, good balance of features and ease

#### Steps:

1. **Connect GitHub**
   - Go to [railway.app](https://railway.app)
   - Create new project
   - Connect GitHub repository

2. **Configure Services**
   - Railway auto-detects Next.js
   - Adds Node.js environment
   - Configure MongoDB database (optional)

3. **Set Environment Variables**
   - In Railway dashboard
   - Add MONGODB_URI, Stripe keys, etc.

4. **Deploy**
   - Auto-deploys on push to main
   - View at provided Railway domain

#### Advantages:
- ✅ Easy setup
- ✅ Auto-deployments
- ✅ Database hosting available
- ✅ Free tier available

#### Cost: Free tier, $5+/month for production

---

## 🔐 Security Configuration

### Environment Variables (Production)
```env
# Production URLs
MONGODB_URI=mongodb+srv://prod_user:password@prod-cluster.mongodb.net/masjid-prod
NEXT_PUBLIC_API_URL=https://your-domain.com

# Security
STRIPE_SECRET_KEY=sk_live_***
STRIPE_PUBLISHABLE_KEY=pk_live_***

# Optional
NEXTAUTH_SECRET=random-secure-string
NEXTAUTH_URL=https://your-domain.com
```

### Security Checklist
- [ ] Use environment variables for all secrets
- [ ] Enable HTTPS/SSL (required)
- [ ] Configure CORS properly
  ```typescript
  // next.config.ts
  headers: [
    {
      key: 'Access-Control-Allow-Origin',
      value: 'https://your-domain.com'
    }
  ]
  ```

- [ ] Set security headers
  ```typescript
  headers: [
    {
      key: 'X-Content-Type-Options',
      value: 'nosniff'
    },
    {
      key: 'X-Frame-Options',
      value: 'DENY'
    },
    {
      key: 'X-XSS-Protection',
      value: '1; mode=block'
    }
  ]
  ```

- [ ] Configure rate limiting
- [ ] Enable authentication
- [ ] Implement logging
- [ ] Set up monitoring

---

## 📊 Monitoring & Logging

### Application Monitoring
```bash
# Install monitoring package
npm install @vercel/analytics

# Enable in app
import { Analytics } from '@vercel/analytics/react';

export default function App() {
  return (
    <>
      <YourApp />
      <Analytics />
    </>
  );
}
```

### Error Tracking
```bash
# Install Sentry for error tracking
npm install @sentry/nextjs

# Configure
export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    await import('@/instrumentation.node');
  }
}
```

### Database Monitoring
- MongoDB Atlas: Built-in monitoring available
- Set up alerts for high query times
- Monitor connection pool usage
- Set up automated backups

---

## 📈 Performance Optimization

### Image Optimization
```tsx
import Image from 'next/image';

<Image
  src="/logo.png"
  alt="Logo"
  width={200}
  height={200}
  priority
/>
```

### CSS Optimization
```bash
# Tailwind CSS automatically purges unused styles
npm run build
```

### Code Splitting
Next.js automatically code-splits at the route level. Lazy load heavy components:

```tsx
import dynamic from 'next/dynamic';

const HeavyComponent = dynamic(() => import('@/components/Heavy'), {
  loading: () => <p>Loading...</p>
});
```

---

## 🔄 CI/CD Setup

### GitHub Actions Example

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
      
      - run: npm ci
      - run: npm run build
      - run: npm run test
      
      - name: Deploy to Vercel
        run: vercel --prod --token ${{ secrets.VERCEL_TOKEN }}
```

---

## 📋 Post-Deployment Checklist

- [ ] Verify dashboard loads at `/dashboard`
- [ ] Test API endpoints
- [ ] Verify database connectivity
- [ ] Test payment flow (if enabled)
- [ ] Check responsive design on mobile
- [ ] Verify PWA installation works
- [ ] Test authentication flow
- [ ] Monitor error logs
- [ ] Set up uptime monitoring
- [ ] Configure backup schedule
- [ ] Document access procedures
- [ ] Create incident response plan

---

## 🚨 Troubleshooting

### Build Fails
```bash
# Clear build cache
rm -rf .next

# Clean reinstall
rm -rf node_modules package-lock.json
npm install
npm run build
```

### Database Connection Error
```
ERROR: Could not connect to MongoDB
```
- Verify MongoDB URI in `.env.local`
- Check IP whitelist in MongoDB Atlas
- Verify credentials are correct
- Check network connectivity

### Application Crashes
```
npm logs show application crashed
```
- Check error logs in PM2/Docker
- Verify all environment variables are set
- Check database connectivity
- Review recent code changes

### High Memory Usage
```
Memory usage > 1GB
```
- Check for memory leaks
- Review long-running operations
- Implement caching
- Scale up instance size

---

## 📞 Support

### Getting Help
1. Check deployment logs
2. Review error messages
3. Consult documentation
4. Check known issues

### Common Issues

**Q: CORS errors?**
A: Configure CORS in `next.config.ts`

**Q: 502 Bad Gateway?**
A: Check if app is running, review logs

**Q: Slow performance?**
A: Check database queries, enable caching

**Q: Database quota exceeded?**
A: Scale up MongoDB plan or optimize queries

---

## 📚 Additional Resources

- [Next.js Deployment Docs](https://nextjs.org/docs/app/building-your-application/deploying)
- [Vercel Documentation](https://vercel.com/docs)
- [MongoDB Atlas Docs](https://docs.atlas.mongodb.com)
- [AWS EC2 Docs](https://docs.aws.amazon.com/ec2)

---

**Last Updated**: January 2024
**Deployment Version**: 1.0
**Status**: Ready for Production

Good luck with your deployment! 🚀
