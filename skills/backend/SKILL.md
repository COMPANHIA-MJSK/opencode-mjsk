# Backend Development Skill

## Purpose
Expert backend development with Node.js and Python. REST APIs, GraphQL, WebSockets, authentication, and database integration.

## Node.js/Express

### API Structure
```typescript
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import { rateLimit } from 'express-rate-limit';

const app = express();

// Middleware
app.use(helmet());
app.use(cors());
app.use(express.json());
app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));

// Routes
app.use('/api/users', userRoutes);
app.use('/api/posts', postRoutes);

// Error handling
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something went wrong!' });
});

app.listen(3000);
```

### TypeScript Controllers
```typescript
import { Request, Response } from 'express';
import { z } from 'zod';

const CreateUserSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  password: z.string().min(8),
});

export const createUser = async (req: Request, res: Response) => {
  try {
    const data = CreateUserSchema.parse(req.body);
    const user = await UserService.create(data);
    res.status(201).json(user);
  } catch (error) {
    if (error instanceof z.ZodError) {
      return res.status(400).json({ errors: error.errors });
    }
    res.status(500).json({ error: 'Internal server error' });
  }
};

export const getUsers = async (req: Request, res: Response) => {
  const { page = 1, limit = 10 } = req.query;
  const users = await UserService.findAll(+page, +limit);
  res.json(users);
};
```

### Database (Prisma)
```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

// Create
const user = await prisma.user.create({
  data: {
    name: 'John',
    email: 'john@example.com',
    posts: {
      create: [{ title: 'First Post', content: 'Hello World' }],
    },
  },
});

// Read with relations
const users = await prisma.user.findMany({
  include: { posts: true },
  where: { posts: { some: { published: true } } },
  orderBy: { createdAt: 'desc' },
  take: 10,
});

// Update
await prisma.user.update({
  where: { id: 1 },
  data: { name: 'Jane' },
});

// Delete
await prisma.user.delete({ where: { id: 1 } });
```

## Python/FastAPI

### API Structure
```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel, EmailStr
from sqlalchemy.orm import Session
from typing import Optional
from datetime import datetime

app = FastAPI()

class UserCreate(BaseModel):
    name: str
    email: EmailStr
    password: str

class UserResponse(BaseModel):
    id: int
    name: str
    email: str
    created_at: datetime
    
    class Config:
        from_attributes = True

@app.post("/users", response_model=UserResponse, status_code=201)
async def create_user(user: UserCreate, db: Session = Depends(get_db)):
    db_user = await UserService.create(db, user)
    return db_user

@app.get("/users", response_model=list[UserResponse])
async def get_users(
    skip: int = 0,
    limit: int = 100,
    db: Session = Depends(get_db)
):
    users = await UserService.get_all(db, skip, limit)
    return users

@app.get("/users/{user_id}", response_model=UserResponse)
async def get_user(user_id: int, db: Session = Depends(get_db)):
    user = await UserService.get_by_id(db, user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user
```

### Authentication
```python
from fastapi.security import OAuth2PasswordBearer
from jose import JWTError, jwt
from passlib.context import CryptContext

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"

def create_token(data: dict):
    to_encode = data.copy()
    to_encode["exp"] = datetime.utcnow() + timedelta(hours=24)
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

async def get_current_user(
    token: str = Depends(oauth2_scheme),
    db: Session = Depends(get_db)
):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        user_id = payload.get("sub")
        if user_id is None:
            raise HTTPException(status_code=401)
    except JWTError:
        raise HTTPException(status_code=401)
    
    user = await UserService.get_by_id(db, user_id)
    if not user:
        raise HTTPException(status_code=401)
    return user
```

## GraphQL (Apollo Server)

```typescript
import { ApolloServer, gql } from 'apollo-server';

const typeDefs = gql`
  type User {
    id: ID!
    name: String!
    email: String!
    posts: [Post!]!
  }
  
  type Post {
    id: ID!
    title: String!
    content: String!
    author: User!
  }
  
  type Query {
    users: [User!]!
    user(id: ID!): User
  }
  
  type Mutation {
    createUser(name: String!, email: String!): User!
  }
`;

const resolvers = {
  Query: {
    users: () => UserService.findAll(),
    user: (_, { id }) => UserService.findById(id),
  },
  Mutation: {
    createUser: (_, args) => UserService.create(args),
  },
  User: {
    posts: (user) => PostService.findByUserId(user.id),
  },
};

const server = new ApolloServer({ typeDefs, resolvers });
server.listen().then(({ url }) => console.log(`Server ready at ${url}`));
```

## Best Practices
- Use environment variables for config
- Implement proper logging (Winston, Pino)
- Use connection pooling for databases
- Implement request validation
- Use proper HTTP status codes
- Implement rate limiting
- Use caching (Redis)
- Write integration tests
- Document APIs (Swagger/OpenAPI)
