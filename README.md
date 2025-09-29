# Blog Automation System

An automated blog creation and publishing system that integrates with WordPress using AI-powered content generation.

## Overview

This system allows users to schedule blog posts which are automatically generated using AI, enhanced with images, and published to WordPress. The system maintains user sessions and tracks blog status across the entire workflow.

## Architecture

### Frontend (Static HTML Files)
- `index.html` - User authentication and WordPress credential setup
- `dashboard.html` - Blog management dashboard with real-time status
- `add-blog.html` - Blog creation form for scheduling new posts

### Backend (N8N Workflows)
The backend consists of multiple N8N workflows hosted at `trupricer.app.n8n.cloud`:

#### User Management Workflow
- **setup-user webhook** (`/webhook/setup-user`)
  - Handles user authentication
  - Creates new users or returns existing user credentials
  - Generates consistent user_id for cross-collection referencing
  - Stores WordPress credentials securely

#### Blog Management Workflow
- **Blog creation webhook** (`/webhook/20aeb809-e341-4129-b40d-a085f152c1a8`)
  - Receives blog details from frontend
  - Stores blog information with status "pending"
  - Links blog to user via user_id

- **Get user blogs webhook** (`/webhook/get-user-blogs`)
  - Retrieves all blogs for a specific user
  - Returns blog status, publish dates, and published URLs
  - Enables dashboard functionality

#### Automated Publishing Workflow
- **Scheduled trigger** (Daily execution)
  - Finds blogs scheduled for current date with "pending" status
  - Processes each blog through AI generation pipeline
  - Updates blog status to "published" with live URLs

## Workflow Process

### User Registration/Login
1. User enters WordPress credentials on index.html
2. Frontend sends credentials to setup-user webhook
3. N8N checks if user exists in users collection
4. Returns existing user_id or creates new user with consistent user_id
5. Frontend stores user_id in localStorage and redirects to dashboard

### Blog Creation
1. User fills blog form on add-blog.html
2. Frontend sends blog data with user_id to blog creation webhook
3. N8N stores blog in blog_fields collection with status "pending"
4. User can view all their blogs on dashboard via get-user-blogs webhook

### Automated Publishing
1. Daily scheduled trigger executes N8N workflow
2. System queries blog_fields collection for today's pending blogs
3. For each blog, system:
   - Retrieves user's WordPress credentials
   - Generates SEO keywords using OpenAI
   - Creates blog title and outline
   - Generates full content using AI agents
   - Creates multiple images using DALL-E
   - Publishes to WordPress via REST API
   - Updates blog status to "published" with live URL

## Database Collections

### users
- user_id (consistent identifier)
- wp_url (WordPress site URL)
- wp_username (WordPress username)
- wp_password (WordPress application password)

### blog_fields
- user_id (links to users collection)
- topic, category, tone, word_count
- publish_date (scheduling date)
- status ("pending" or "published")
- published_url (live blog URL after publishing)
- created_at (timestamp)

## API Endpoints

All endpoints are hosted at `https://trupricer.app.n8n.cloud`:

- `POST /webhook/setup-user` - User authentication and registration
- `POST /webhook/20aeb809-e341-4129-b40d-a085f152c1a8` - Create new blog
- `POST /webhook/get-user-blogs` - Retrieve user's blogs

## Technologies Used

### Frontend
- HTML5, CSS3, JavaScript (Vanilla)
- Local storage for session management
- Fetch API for backend communication

### Backend
- N8N workflow automation
- MongoDB for data persistence
- OpenAI GPT-4 for content generation
- DALL-E for image generation
- WordPress REST API for publishing

### Deployment
- Render.com for static site hosting
- N8N Cloud for workflow execution
- MongoDB Atlas for database hosting

## Security Features

- WordPress application passwords (not main passwords)
- User isolation via user_id system
- Secure credential storage in MongoDB
- HTTPS encryption for all communications

## Running Command

- python -m http.server 8000

## Deployment

This frontend is deployed as a static site on Render.com. The N8N workflows run independently on N8N Cloud infrastructure, ensuring reliable automation execution.