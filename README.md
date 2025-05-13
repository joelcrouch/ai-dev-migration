# AI-Powered Code Migration Assistant

A full-stack application that helps developers automatically migrate code between different frameworks using AI-powered transformations.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Version](https://img.shields.io/badge/version-0.1.0-green.svg)

## 📋 Overview

The AI-Powered Code Migration Assistant is a developer tool that uses machine learning to automate the process of migrating code between different frameworks and libraries. The system analyzes source code, identifies migration patterns, and generates equivalent code in the target framework while maintaining functionality and best practices.

**Key Features:**
- 🔄 Convert between popular frameworks (e.g., Angular → React, jQuery → Vue)
- 🤖 AI-powered code analysis and transformation
- 📊 Visual comparison of before/after code
- 🔍 Automatic identification of compatibility issues
- 📝 Smart suggestions for manual resolution when needed
- 📈 Migration quality metrics and reporting

## 🛠️ Tech Stack

### Backend
- Node.js & Express
- MongoDB (for migration templates and history)
- JWT authentication
- Queue system (Bull with Redis)

### Frontend
- Next.js & React
- Tailwind CSS for styling
- Monaco Editor for code editing
- React Query for data fetching

### AI Integration
- OpenAI API integration
- Custom prompting system
- Caching layer for common transformations

## 🚀 Getting Started

### Prerequisites
- Node.js (v18 or higher)
- npm or yarn
- MongoDB instance
- Redis server (for queuing)
- OpenAI API key

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/ai-code-migration.git
   cd ai-code-migration
   ```

2. **Install dependencies for both backend and frontend**
   ```bash
   # Install backend dependencies
   cd server
   npm install
   
   # Install frontend dependencies
   cd ../client
   npm install
   ```

3. **Set up environment variables**

   Create a `.env` file in the `server` directory:
   ```
   PORT=4000
   MONGODB_URI=mongodb://localhost:27017/code-migration
   JWT_SECRET=your_jwt_secret
   OPENAI_API_KEY=your_openai_api_key
   REDIS_URL=redis://localhost:6379
   ```

   Create a `.env.local` file in the `client` directory:
   ```
   NEXT_PUBLIC_API_URL=http://localhost:4000/api
   ```

4. **Start the development servers**

   For the backend:
   ```bash
   cd server
   npm run dev
   ```

   For the frontend:
   ```bash
   cd client
   npm run dev
   ```

5. **Access the application**
   
   Open your browser and navigate to `http://localhost:3000`

## 🔧 Configuration

### Backend Configuration
- Adjust rate limiting in `server/config/rateLimiter.js`
- Configure authentication settings in `server/config/auth.js`
- Modify AI provider settings in `server/config/ai.js`

### Frontend Configuration
- Update theme settings in `client/tailwind.config.js`
- Configure code editor options in `client/components/CodeEditor/config.js`

## 📊 Project Structure

```
ai-code-migration/
├── server/                  # Backend Node.js application
│   ├── controllers/         # Request handlers
│   ├── middleware/          # Express middleware
│   ├── models/              # MongoDB models
│   ├── routes/              # API routes
│   ├── services/            # Business logic
│   │   ├── ai/              # AI integration
│   │   ├── migration/       # Migration engine
│   │   └── analysis/        # Code analysis tools
│   ├── utils/               # Helper functions
│   └── server.js            # Entry point
│
├── client/                  # Frontend Next.js application
│   ├── components/          # React components
│   │   ├── Dashboard/       # Dashboard UI
│   │   ├── CodeEditor/      # Code editing interface
│   │   └── Comparison/      # Before/after comparison
│   ├── context/             # React context
│   ├── hooks/               # Custom React hooks
│   ├── pages/               # Next.js pages
│   ├── public/              # Static assets
│   └── styles/              # CSS and styling
│
└── README.md                # Project documentation
```

## 🗺️ Project Roadmap

### Phase 1: Foundation (Weeks 1-2)
- [x] Set up project structure and core dependencies
- [ ] Implement basic Express server with MongoDB connection
- [ ] Create Next.js frontend with basic layout
- [ ] Set up authentication system (JWT)
- [ ] Implement code editor component with syntax highlighting
- [ ] Create basic API endpoints for code submission

### Phase 2: Core Functionality (Weeks 3-4)
- [ ] Develop AI service integration for code analysis
- [ ] Build code transformation pipeline
- [ ] Create migration result visualization
- [ ] Implement basic dashboard UI
- [ ] Set up queue system for handling migration requests
- [ ] Add user account management

### Phase 3: Advanced Features (Weeks 5-6)
- [ ] Implement compatibility issue detection
- [ ] Add support for multiple framework migrations
- [ ] Create template system for common migration patterns
- [ ] Build metrics collection and reporting
- [ ] Add caching layer for performance optimization
- [ ] Implement real-time migration status updates

### Phase 4: Polish & Optimization (Weeks 7-8)
- [ ] Add comprehensive error handling
- [ ] Implement automated testing
- [ ] Optimize performance for large codebases
- [ ] Add migration history and version control
- [ ] Create user documentation
- [ ] Implement feedback collection system

### Future Enhancements
- [ ] Support for additional languages beyond JavaScript
- [ ] Batch migration for entire projects
- [ ] ML model fine-tuning based on feedback
- [ ] Integration with code repositories (GitHub, GitLab)
- [ ] CI/CD pipeline integration
- [ ] IDE plugins

## 🧪 Testing

Run backend tests:
```bash
cd server
npm test
```

Run frontend tests:
```bash
cd client
npm test
```

## 🔍 API Documentation

API documentation is available at `http://localhost:4000/api-docs` when running the development server.
(maybe not sure about fastapi or something else, right now no fastapi)
Key endpoints:
- `POST /api/migrations` - Submit code for migration
- `GET /api/migrations/:id` - Get migration status and results
- `GET /api/frameworks` - List available framework migrations
- `POST /api/feedback` - Submit feedback on migration quality

## 👥 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgements

- OpenAI for AI capabilities
- The open-source community for various libraries and tools
- All contributors and testers

---

**Note**: This project is for demonstration purposes and highlights skills in Node.js backend development, Next.js/React frontend development, and AI integration - perfect for showcasing abilities needed for an AI-powered migration platform position.