# API Structure for AI-Powered Code Migration Assistant

## Overview

This document outlines the API endpoints for the AI-Powered Code Migration Assistant, including both internal and external APIs.

## External APIs (Frontend to Backend)

### Authentication

| Endpoint | Method | Description | Request Body | Response |
|----------|--------|-------------|--------------|----------|
| `/api/auth/register` | POST | Register a new user | `{ email, password, name }` | `{ token, user }` |
| `/api/auth/login` | POST | Login existing user | `{ email, password }` | `{ token, user }` |
| `/api/auth/verify` | GET | Verify JWT token | - | `{ user }` |

### Migration Projects

| Endpoint | Method | Description | Request Body | Response |
|----------|--------|-------------|--------------|----------|
| `/api/projects` | GET | List all user projects | - | `[{ id, name, sourceFramework, targetFramework, status, ... }]` |
| `/api/projects` | POST | Create new migration project | `{ name, description, sourceFramework, targetFramework }` | `{ id, name, ... }` |
| `/api/projects/:id` | GET | Get project details | - | `{ id, name, files: [...], status, ... }` |
| `/api/projects/:id` | PUT | Update project | `{ name, description, ... }` | `{ id, name, ... }` |
| `/api/projects/:id` | DELETE | Delete project | - | `{ success: true }` |

### Files & Code

| Endpoint | Method | Description | Request Body | Response |
|----------|--------|-------------|--------------|----------|
| `/api/projects/:id/files` | GET | List all files in project | - | `[{ id, name, path, status, ... }]` |
| `/api/projects/:id/files` | POST | Add new file to project | `{ name, path, content, type }` | `{ id, name, ... }` |
| `/api/projects/:id/files/:fileId` | GET | Get file details & content | - | `{ id, name, content, ... }` |
| `/api/projects/:id/files/:fileId` | PUT | Update file | `{ content, name, ... }` | `{ id, name, ... }` |
| `/api/projects/:id/files/:fileId` | DELETE | Delete file | - | `{ success: true }` |

### Migration Operations

| Endpoint | Method | Description | Request Body | Response |
|----------|--------|-------------|--------------|----------|
| `/api/projects/:id/migrate` | POST | Start migration for project | `{ options: { ... } }` | `{ jobId, status }` |
| `/api/projects/:id/files/:fileId/migrate` | POST | Migrate single file | `{ options: { ... } }` | `{ jobId, status }` |
| `/api/migrations/:jobId` | GET | Get migration status | - | `{ status, progress, results }` |
| `/api/migrations/:jobId/cancel` | POST | Cancel ongoing migration | - | `{ success: true }` |

### Frameworks & Options

| Endpoint | Method | Description | Request Body | Response |
|----------|--------|-------------|--------------|----------|
| `/api/frameworks` | GET | List available framework migrations | - | `[{ id, name, version, ... }]` |
| `/api/frameworks/compatibility` | POST | Check framework compatibility | `{ source, target }` | `{ compatible, issues: [...] }` |

### Feedback & Analytics

| Endpoint | Method | Description | Request Body | Response |
|----------|--------|-------------|--------------|----------|
| `/api/feedback` | POST | Submit feedback on migration | `{ migrationId, rating, comments }` | `{ success: true }` |
| `/api/analytics/user` | GET | Get user migration stats | - | `{ totalProjects, successRate, ... }` |

## Internal APIs (Backend Services)

### AI Service

| Method | Description | Parameters | Response |
|--------|-------------|------------|----------|
| `analyzeCode(code, language)` | Analyze code structure | `code`: string, `language`: string | `{ components: [...], complexity: number, ... }` |
| `transformCode(code, sourceFramework, targetFramework, options)` | Transform code from one framework to another | `code`: string, `sourceFramework`: string, `targetFramework`: string, `options`: object | `{ transformed: string, notes: [...], confidence: number }` |
| `identifyIssues(code, targetFramework)` | Identify potential issues in transformed code | `code`: string, `targetFramework`: string | `{ issues: [...], suggestions: [...] }` |

### Migration Engine

| Method | Description | Parameters | Response |
|--------|-------------|------------|----------|
| `startMigration(projectId, options)` | Start migration process | `projectId`: string, `options`: object | `{ jobId: string }` |
| `getMigrationStatus(jobId)` | Get status of migration job | `jobId`: string | `{ status, progress, errors: [...] }` |
| `processFile(fileId, options)` | Process single file migration | `fileId`: string, `options`: object | `{ result, issues: [...] }` |

### Analysis Service

| Method | Description | Parameters | Response |
|--------|-------------|------------|----------|
| `compareCodeSimilarity(original, transformed)` | Compare similarity between original and transformed code | `original`: string, `transformed`: string | `{ similarityScore, functionMatch, ... }` |
| `validateTransformedCode(code, framework)` | Validate transformed code against framework standards | `code`: string, `framework`: string | `{ valid: boolean, warnings: [...] }` |
| `generateTestCases(code, framework)` | Generate test cases for validation | `code`: string, `framework`: string | `{ tests: [...] }` |

## WebSocket Events

| Event | Direction | Description | Payload |
|-------|-----------|-------------|---------|
| `migration:status` | Server → Client | Real-time migration status updates | `{ jobId, status, progress, ... }` |
| `migration:complete` | Server → Client | Migration job completed | `{ jobId, results: [...] }` |
| `migration:error` | Server → Client | Error during migration | `{ jobId, error }` |
