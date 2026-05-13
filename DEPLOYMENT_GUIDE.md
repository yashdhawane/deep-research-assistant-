# Deployment Guide: Deep Research Agent on Render

This guide walks you through deploying the Deep Research Agent on Render.com (a free/paid hosting platform).

## Prerequisites

- [Render](https://render.com/) account (free tier available)
- GitHub repository with this project
- API key from one of these:
  - **Gemini** (free tier) - https://makersuite.google.com/app/apikey
  - **OpenAI** (paid) - https://platform.openai.com/api-keys
  - Both are optional (Gemini is recommended for free deployment)

---

## Step 1: Prepare Your Project

### 1.1 Update .env.example (No secrets!)
The `.env.example` file should **NEVER** contain actual API keys. ✅ Already done!

### 1.2 Clear Sensitive Data
Remove any secrets from your git history:

```bash
# Remove .env from git tracking
git rm --cached .env
echo ".env" >> .gitignore
git add .gitignore
git commit -m "Remove sensitive .env file"

# Push to GitHub
git push origin main
```

### 1.3 Push to GitHub

```bash
git add .
git commit -m "Prepare for Render deployment"
git push origin main
```

---

## Step 2: Create Render Account & Connect GitHub

1. Go to [render.com](https://render.com/)
2. Sign up (use GitHub for easier setup)
3. Click **"+ New"** → **"Web Service"**
4. Select **"Connect a GitHub repository"**
5. Authorize Render to access your GitHub
6. Select the repository containing this project

---

## Step 3: Configure Render Deployment

### 3.1 Basic Settings

| Setting | Value |
|---------|-------|
| **Name** | `deep-research-agent` (or your preferred name) |
| **Environment** | Python 3.11+ |
| **Region** | Choose closest to you |
| **Plan** | Free (or Starter if you want better performance) |

### 3.2 Build Settings

| Setting | Value |
|---------|-------|
| **Build Command** | `pip install -r requirements.txt` |
| **Start Command** | `chainlit run app.py --host 0.0.0.0 --port $PORT` |

### 3.3 Environment Variables

Click **"Advanced"** → **"Add Environment Variable"** and add:

```
MODEL_PROVIDER=gemini
GEMINI_API_KEY=your_actual_api_key_here
MODEL_NAME=gemini-2.5-flash
SUMMARIZATION_MODEL=gemini-2.5-flash
SEARCH_PROVIDER=duckduckgo
MAX_SEARCH_QUERIES=5
MAX_SEARCH_RESULTS_PER_QUERY=5
MAX_REPORT_SECTIONS=10
CITATION_STYLE=apa
CHAINLIT_HOST=0.0.0.0
```

⚠️ **Important:** Paste your actual **GEMINI_API_KEY** in the environment variables on Render, NOT in the code!

### 3.4 Instance Settings (Optional)

For better performance on Free plan:
- **Memory**: 512MB (default)
- **vCPU**: 0.5

---

## Step 4: Deploy

1. Click **"Create Web Service"**
2. Render will automatically:
   - Clone your repository
   - Install dependencies
   - Start the application
   - Assign a URL (e.g., `https://deep-research-agent.onrender.com`)

3. **Wait for deployment** (2-5 minutes)

4. Your app will be live at the provided URL!

---

## Step 5: Verify Deployment

1. Visit your Render URL
2. You should see the Chainlit web interface
3. Try a research query to verify everything works

---

## Troubleshooting

### ❌ "Module not found" errors

**Solution**: Check if all dependencies are in `requirements.txt`:

```bash
pip freeze > requirements.txt
git add requirements.txt
git commit -m "Update requirements"
git push
```

### ❌ "GEMINI_API_KEY not found"

**Solution**: 
1. Go to Render Dashboard → Your App → Settings
2. Scroll to Environment Variables
3. Make sure `GEMINI_API_KEY` is set
4. Redeploy: Click **"Manual Deploy"** → **"Deploy latest commit"**

### ❌ "Application crashed" / "Exit code 1"

**Solution**: 
1. Check logs: Dashboard → **"Logs"** tab
2. Look for error messages
3. Common issues:
   - Missing API key
   - Wrong Python version (need 3.11+)
   - Memory limit exceeded (upgrade plan)

### ❌ "Timeout" errors during research

**Solution**: 
- This is normal on Free tier (limited resources)
- Upgrade to Starter plan for better performance
- Or reduce `MAX_SEARCH_QUERIES` in environment variables

---

## Performance Optimization

### For Free Plan:
```env
MAX_SEARCH_QUERIES=3
MAX_SEARCH_RESULTS_PER_QUERY=3
MAX_REPORT_SECTIONS=5
```

### For Starter Plan:
```env
MAX_SEARCH_QUERIES=5
MAX_SEARCH_RESULTS_PER_QUERY=5
MAX_REPORT_SECTIONS=10
```

### For Pro Plan:
```env
MAX_SEARCH_QUERIES=10
MAX_SEARCH_RESULTS_PER_QUERY=10
MAX_REPORT_SECTIONS=15
```

---

## Using Alternative LLM Providers

### Option 1: OpenAI
```env
MODEL_PROVIDER=openai
OPENAI_API_KEY=sk-...
OPENAI_BASE_URL=https://api.openai.com
MODEL_NAME=gpt-4-turbo
```

### Option 2: Local Ollama (not recommended for Render)
```env
MODEL_PROVIDER=ollama
OLLAMA_BASE_URL=http://localhost:11434
MODEL_NAME=qwen2.5:7b
```

---

## Security Best Practices

✅ **DO:**
- Store API keys in Render environment variables only
- Never commit `.env` files to GitHub
- Use `.env.example` template
- Rotate API keys regularly
- Use free tier APIs when possible (Gemini offers free tier)

❌ **DON'T:**
- Hardcode API keys in code
- Share your API keys publicly
- Commit `.env` to GitHub
- Use same key across multiple projects

---

## Custom Domain (Optional)

1. Go to Render Dashboard → Your App → Settings
2. Scroll to **"Custom Domain"**
3. Add your domain (e.g., `research.yourdomain.com`)
4. Follow DNS setup instructions

---

## Monitoring & Logs

**To view logs:**
1. Dashboard → Your App
2. Click **"Logs"** tab
3. Scroll to see real-time logs

**To monitor resource usage:**
1. Dashboard → Your App → **"Metrics"** tab
2. View CPU, Memory, and request metrics

---

## Updating Your App

To push updates:

```bash
# Make changes locally
git add .
git commit -m "Your changes"
git push origin main
```

Render will automatically redeploy! (usually within 1 minute)

---

## Deleting Your App

If you want to remove the deployment:
1. Dashboard → Your App → Settings
2. Scroll to bottom → **"Delete Web Service"**
3. Confirm deletion

---

## Cost Breakdown

| Plan | Price/Month | Free Tier? | Suitable For |
|------|-------------|-----------|--------------|
| **Free** | $0 | ✅ Yes | Testing, light use |
| **Starter** | $7 | ❌ No | Production, low traffic |
| **Standard** | $12 | ❌ No | Regular production |
| **Pro** | $29+ | ❌ No | High-traffic production |

**Note:** Add API costs (Gemini is free tier, OpenAI ~$0.01 per query)

---

## Need Help?

- Render Documentation: https://render.com/docs
- LangChain Docs: https://python.langchain.com/
- Chainlit Docs: https://docs.chainlit.io/
