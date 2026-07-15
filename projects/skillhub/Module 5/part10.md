Sl No	Topic	Concepts Covered
1	Python Essentials	Variables, data types, operators, control flow, functions, scope, error handling, file I/O
2	Functional Programming in Python	Function definitions, parameters and arguments, default and keyword arguments, *args and **kwargs, scope and closures, lambdas, higher-order functions, map, filter, reduce, functools.partial, when to reach for these tools
3	Object-Oriented Python	Classes, instances, inheritance, encapsulation, dunder methods, class vs instance methods, modules & packages, virtual environments
4	Python's Power Features	Decorators, comprehensions, generators, iterators, type hints, typing module, context managers, intro to async syntax
5	HTML Foundations	HTML structure, semantic tags, common elements, forms and inputs, accessibility basics, DevTools (Elements tab)
6	CSS Fundamentals	CSS selectors, the box model, typography, colors, basic layout, specificity and the cascade
7	CSS Layout: Flexbox & Grid	Flexbox in depth, CSS Grid, responsive design, media queries, building common layout patterns
8	JavaScript Essentials	JS syntax, variables (let/const), operators, control flow, functions, scope, hoisting basics
9	JavaScript: Working with Data	Arrays and array methods (map, filter, reduce, find), objects, destructuring, spread/rest, ES6 modules
10	JavaScript: DOM & Events	DOM tree, querying elements, manipulating DOM, event listeners, event bubbling basics, building interactive page features
11	JavaScript: Async & APIs	Callbacks, Promises, async/await, fetch(), working with JSON, error handling in async code, building a small page that talks to a public API
12	Version Control with Git & GitHub	Why version control exists, git init/add/commit, the staging area, .gitignore, history with log/diff, branches and merge, conflicts, GitHub basics (remote, push, pull, clone), pull requests as collaboration
13	Backend & API Fundamentals	What is backend, client-server model, HTTP protocol, request-response lifecycle, REST principles, JSON, status codes, monolith vs microservices
14	FastAPI Introduction	FastAPI setup, Uvicorn, ASGI vs WSGI, Starlette overview, project initialization, first route, Swagger/ReDoc auto-docs, FastAPI vs Flask/Django
15	Routing & Async Basics	Path params, query params, request body basics, async def vs def routes, static vs dynamic routes, route validation, error handling with HTTPException
16	HTTP Methods & CRUD APIs	GET, POST, PUT, PATCH, DELETE semantics, building a full CRUD API with in-memory storage, response formatting, status code conventions
17	Pydantic Fundamentals	Pydantic v2 schemas, request/response models, basic validation, Field, Annotated
18	Pydantic Advanced	Nested models, custom validators, computed fields, model_config, serialization control, common patterns
19	Dependency Injection	Depends(), dependency functions, sub-dependencies, dependency caching, common DI patterns in FastAPI
20	Jinja2 & Server-Rendered Views	HTML templates, Jinja2 syntax, template inheritance, rendering data from FastAPI, forms posting to FastAPI endpoints, when to use server-rendered vs SPA
21	Project Structure & Routers	APIRouter, modular architecture, separation into routers/services/schemas/models/configs, environment variables with pydantic-settings
22	API Documentation & Versioning	Writing meaningful endpoint descriptions, request/response examples, tags and grouping, OpenAPI customization, versioning strategies, deprecation patterns, generating client SDKs
23	Database Fundamentals	Relational DB concepts, SQL basics (SELECT, INSERT, UPDATE, DELETE, JOIN), SQLite vs PostgreSQL, when to use which
24	SQLAlchemy I: ORM Basics	ORM concepts, connection setup, declarative models, sessions and session lifecycle, basic CRUD with SQLAlchemy 2.0
25	SQLAlchemy II: Relationships & Queries	One-to-many, many-to-many, joins, relationship() and back_populates, eager vs lazy loading, query filtering, sorting, aggregations
26	Alembic & Schema Migrations	Alembic setup, autogenerate, migration workflow, schema evolution patterns, handling production migrations
27	NoSQL & MongoDB Fundamentals	SQL vs NoSQL trade-offs, when to choose document databases, MongoDB document model, collections vs tables, BSON, MongoDB shell basics, CRUD operations, query operators ($eq, $gt, $in, $regex), projection, sorting, intro to indexing
28	MongoDB with FastAPI	Async MongoDB drivers (Motor) and ODMs (Beanie), connecting FastAPI to MongoDB, Pydantic models with MongoDB, embedding vs referencing, schema design patterns, aggregation pipeline basics ($match, $group, $lookup, $project), when MongoDB outperforms SQL and when it doesn't
29	Async Deep-Dive	Async/await mental model, event loop, concurrency vs parallelism, async DB with asyncpg/async SQLAlchemy, when async actually helps, common pitfalls
30	Authentication	Password hashing with passlib/bcrypt, JWT structure and signing, OAuth2PasswordBearer, login/register flow end-to-end, token refresh
31	Authorization & Security	Protected routes, Depends(get_current_user), RBAC, middleware, CORS configuration, common vulnerabilities, rate limiting basics
32	File Uploads & Storage	UploadFile, multipart handling, file validation, serving static files, local storage patterns, AWS S3 with Boto3
33	Pagination, Filtering & Query Optimization	Offset vs cursor pagination, query parameters for filtering and sorting, indexing concepts, N+1 problem and how to avoid it
34	Caching with Redis	Redis basics, when to cache vs when not to, cache key design, TTL, FastAPI integration with fastapi-cache, invalidation patterns, cache stampede
35	Background Tasks & Email Workflows	BackgroundTasks, when to use them vs Celery/RQ (conceptual), sending email via SMTP, password reset workflow with token expiry
36	React Introduction	Why React exists, virtual DOM concept, setting up with Vite, JSX, first component, project structure
37	Components & Props	Functional components, props, prop types, composition, children, conditional rendering
38	State & Events	useState, event handlers, controlled inputs, lifting state up, derived state
39	Effects & Lifecycle	useEffect, dependency arrays, cleanup functions, common effect patterns and pitfalls
40	Lists, Forms & Validation	Rendering lists, keys, controlled forms, form validation with React Hook Form and Zod, multi-step forms
41	Fetching Data from APIs	fetch and axios in React, loading and error states, useEffect for data fetching, intro to React Query/TanStack Query
42	React Router	Client-side routing, routes and links, nested routes, route params, navigation, protected routes
43	Context & Global State	useContext, when to use context, building an auth context, intro to state management options (Zustand, Redux Toolkit)
44	Styling React Apps	CSS modules, Tailwind CSS basics, component libraries (shadcn/ui or similar), responsive React layouts
45	Building & Deploying React	Vite build process, environment variables, deploying to Vercel/Netlify, build optimization basics
46	Full-Stack Integration I: Connecting React to FastAPI	CORS in practice, API client setup, environment configs across frontend/backend, handling errors across the stack
47	Full-Stack Integration II: Auth Flow	Login/register UI in React, storing tokens (localStorage vs httpOnly cookies trade-offs), attaching auth headers, protected routes on both ends, refresh token handling
48	Full-Stack Integration III: Building a Feature End-to-End	Take one CRUD resource and build it fully: DB model, API, React UI with create/read/update/delete, including loading and error states
49	WebSockets & Real-Time	FastAPI WebSocket support, React WebSocket clients, building a live feature (chat or notifications), connection lifecycle
50	External API Integration	Calling third-party APIs from FastAPI with httpx, handling timeouts and retries, mocking external calls in tests, API key management
51	Testing FastAPI I: Unit & Integration	Pytest deep-dive, advanced fixtures, test database setup and teardown, testing services and routes
52	Testing FastAPI II: Auth & Mocking	Testing auth-protected routes, mocking external services, parametrized tests, coverage reporting
53	Testing React	Vitest, React Testing Library, testing components, testing hooks, mocking API calls, end-to-end testing intro (Playwright)
54	Logging & Observability	Python's logging module, structured logging with loguru or structlog, request logging middleware, correlation IDs, error tracking with Sentry, reading production logs
55	Docker & Containerization	Docker concepts, Dockerfile for FastAPI, multi-stage builds, docker-compose for app + DB + Redis + frontend, environment configs
56	Cloud Deployment & CI/CD	Cloud container options (Render/Railway/Fly.io), VPS deployment with Nginx + SSL, GitHub Actions for automated testing and deployment, environment management, production checklist
57	Talking to LLMs in Python	Working with an LLM APIs, the chat completion model, message format and roles, system instructions, generation parameters (temperature, max tokens), single-turn vs multi-turn conversations, streaming responses to the terminal, handling API errors and retries, token and cost awareness, building a multi-turn CLI chat application
58	Tool Calling & Function Execution	What tool calling is and why it makes LLMs useful beyond chat, declaring tools as Python functions, JSON schemas for tool definitions, the call-execute-respond loop, building agents that can run multiple tools (file system access, calculator, public API calls), handling multi-step tool workflows, error handling in tool execution, when tool calling beats traditional code
59	RAG: Retrieval-Augmented Generation	Why RAG exists (grounding LLMs in your data), embeddings and vector representations, vector databases (Chroma for local simplicity), chunking strategies, similarity search, building a Python RAG pipeline that answers questions over a folder of documents, evaluating retrieval quality, common failure modes (chunk size, retrieval precision, hallucination)
60	Building a Complete Agent	Combining everything into a working Python agent: chat, tool calling, RAG, multi-turn memory. Persisting conversation history with SQLite. Agent design patterns implemented in plain Python: single-agent loops, router patterns, simple supervisor patterns. Production considerations: prompt injection awareness, cost monitoring, observability with simple logging, when to use agents vs traditional code. 
