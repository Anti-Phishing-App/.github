# Software Design Specification (SDS)
## 피싱 탐지 시스템 (Phishing Detection System)

## Content
1. [Introduction](#1-introduction)
2. [Use Case Analysis](#2-use-case-analysis)
3. [Class Diagrams](#3-class-diagrams)
4. [Sequence Diagrams](#4-sequence-diagrams)
5. [State Machine Diagrams](#5-state-machine-diagrams)
6. [User Interface Design](#6-user-interface-design)
7. [Implementation Requirements](#7-implementation-requirements)
8. [Glossary](#8-glossary)
9. [References](#9-references)

## Authors for each section

Introduction

Use Case Analysis

Class Diagrams

Sequence Diagrams

State Machine Diagrams

User Interface Design

Implementation Requirements

Glossary

References

## 1. Introduction

## 2. Use Case Analysis

### 2.1 Use Case Diagram

### 2.2 Use Case Descriptions

## 3. Class Diagrams

## 4. Sequence Diagrams

## 5. State Machine Diagrams

## 6. User Interface Design

## 7. Implementation Requirements

### 7.1 Technology Stack

#### Backend
- **Framework**: FastAPI 0.100+
- **Python**: 3.9+
- **ASGI Server**: Uvicorn

#### Database
- **ORM**: SQLAlchemy 2.0+
- **Database**: SQLite (development), PostgreSQL (production)
- **Migration**: Alembic (recommended)

#### AI/ML
- **Deep Learning**: PyTorch 2.0+
- **NLP Model**: KoBERT (kobert-transformers)
- **Tokenizer**: transformers (BertTokenizer)
- **Morphological Analysis**: KoNLPy (Okt)
- **STT**: CLOVA Speech API (REST + gRPC)

#### Security
- **Authentication**: JWT (HS256)
- **Password Hashing**: bcrypt (via passlib)
- **Token Management**: python-jose

#### Communication
- **HTTP Client**: httpx (async)
- **WebSocket**: FastAPI native WebSocket
- **gRPC**: grpcio, grpcio-tools

### 7.2 System Requirements

#### Hardware (Minimum)
- CPU: 4 cores
- RAM: 8GB
- Storage: 10GB
- GPU: CUDA-compatible GPU (optional, for faster inference)

#### Hardware (Recommended)
- CPU: 8+ cores
- RAM: 16GB+
- Storage: 50GB+ SSD
- GPU: NVIDIA GPU with 8GB+ VRAM (CUDA 11.8+)

### 7.3 Environment Configuration

#### Required Environment Variables (.env)
```bash
# Database
DATABASE_URL=sqlite:///./pyserver.db

# JWT
JWT_SECRET_KEY=
JWT_ALGORITHM=HS256
JWT_ACCESS_TOKEN_EXPIRE_MINUTES=30
JWT_REFRESH_TOKEN_EXPIRE_DAYS=7

# CLOVA Speech API
CLOVA_INVOKE_URL=

CLOVA_SECRET_KEY= #STT용

CLOVA_SECRET_KEY= #OCR용

# File Upload
UPLOAD_DIR=./uploads
MAX_UPLOAD_SIZE=10485760  # 10MB
```

### 7.4 Dependencies (requirements.txt)

```
# Web Framework
fastapi>=0.100.0
uvicorn[standard]>=0.23.0
python-multipart>=0.0.6

# Database
sqlalchemy>=2.0.0
psycopg2-binary>=2.9.0  # PostgreSQL
alembic>=1.11.0  # Migration tool

# Security
python-jose[cryptography]>=3.3.0
passlib[bcrypt]>=1.7.4
pydantic[email]>=2.0.0

# AI/ML
torch>=2.0.0
transformers>=4.30.0
kobert-transformers>=0.5.1
konlpy>=0.6.0
pandas>=2.0.0
numpy>=1.24.0

# HTTP Client
httpx>=0.24.0

# gRPC
grpcio>=1.56.0
grpcio-tools>=1.56.0

# Others
python-dotenv>=1.0.0
Pillow>=10.0.0  # Image processing
```