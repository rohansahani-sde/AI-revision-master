# AI Revision Master
 
A study planner for data structures and algorithms. Enter a topic and a number of days, and it generates a day-wise lesson plan or a flashcard deck using the Gemini API. Plans are saved to your account, and you can set revision reminders for any day.
 
Built with React, Node.js, Express, MongoDB and the Gemini API.
 
## Screenshots


<img width="1896" height="907" alt="image" src="https://github.com/user-attachments/assets/2f7d1d08-cdc8-486b-a8f5-2eea60fbea6b" />

---
<img width="1895" height="908" alt="image" src="https://github.com/user-attachments/assets/67f4966d-ce07-4fd7-b161-10a8d11517a4" />

---



<img width="1896" height="905" alt="image" src="https://github.com/user-attachments/assets/ac234bac-0cff-458f-9919-de0de90bba10" />

---


<img width="1899" height="908" alt="image" src="https://github.com/user-attachments/assets/d5e36c68-b236-406c-b466-2b137c09d674" />

---


<img width="1901" height="911" alt="image" src="https://github.com/user-attachments/assets/28b2344f-6366-4f1a-a248-1d077284de04" />

---

<img width="1896" height="908" alt="image" src="https://github.com/user-attachments/assets/eafd5b46-786b-431f-ba42-7ecc67a812d7" />

---

<img width="1901" height="908" alt="image" src="https://github.com/user-attachments/assets/d1eed0ae-3797-44dd-87d8-f853668a57d3" />

---

<img width="1899" height="906" alt="image" src="https://github.com/user-attachments/assets/d4786afa-1929-4131-a3ac-cad22852ae3f" />

---


<img width="1899" height="908" alt="image" src="https://github.com/user-attachments/assets/881abb97-1476-46d4-be83-fa0018a27972" />


## Features
 
- **Day-wise lesson plans.** For a topic and number of days, each day lists the concepts to cover, a short explanation of each, one practice problem (description, input and output format, examples) and a link to a related LeetCode or GeeksforGeeks problem.
- **Flashcard decks.** Generate a deck for any topic and revise it with animated card flips (Framer Motion).
- **Saved plans.** Lesson plans and flashcard decks are stored in MongoDB per user and can be reopened or deleted.
- **Revision reminders.** Set a reminder for a specific day of a plan. Reminders are stored in the browser, and a desktop notification is shown when you open the app on the reminder date.
- **Authentication.** Register and login with bcrypt-hashed passwords and JWT (7-day expiry). All lesson and generation routes require a valid token.
- **Usage limit.** Each user can run 2 AI generations per day, enforced on the server.
## How it works
 
1. The user picks a topic and number of days in the React client, which builds a prompt that asks for a JSON lesson plan (or a flashcard set).
2. The client sends the prompt to `POST /generate`. The Express backend checks the JWT, applies the daily usage limit, and forwards the prompt to Gemini 2.5 Flash.
3. The client extracts the JSON array from the model response, parses it, and saves it through `POST /api/lessons`.
4. Saved lessons are stored in a MongoDB `Lesson` collection with a `type` of `lesson` or `flashcard`, and loaded again through `GET /api/lessons`.
```
React client  ->  Express API  ->  Gemini 2.5 Flash
                      |
                   MongoDB
```
 
## Tech stack
 
| Layer | Technology |
|---|---|
| Frontend | React 19, Vite, Tailwind CSS 3, React Router, Framer Motion, Axios |
| Backend | Node.js, Express 5, Mongoose, JWT, bcryptjs |
| Database | MongoDB |
| AI | Gemini API (gemini-2.5-flash) |
 
## API
 
All routes except register and login require an `Authorization: Bearer <token>` header.
 
| Method | Route | Description |
|---|---|---|
| POST | `/api/auth/register` | Create an account |
| POST | `/api/auth/login` | Log in and receive a JWT |
| POST | `/generate` | Send a prompt to Gemini (limited to 2 per user per day) |
| GET | `/api/lessons` | List saved plans, optionally filtered with `?type=lesson` or `?type=flashcard` |
| POST | `/api/lessons` | Save a lesson plan or flashcard deck |
| DELETE | `/api/lessons/:id` | Delete a saved item |
 
## Getting started
 
### Prerequisites
 
- Node.js 18 or later
- A MongoDB database (Atlas or local)
- A Gemini API key
### Backend

```
cd backend
npm install
```
 
Create `backend/.env`:
 
```
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=a_long_random_secret
GEMINI_API_KEY=your_gemini_api_key
PORT=5000
```
 
Start the server:
 
```
node server.js
```
 
### Client
 
```
cd client
npm install
npm run dev
```
 
The client expects the backend at `http://localhost:5000`.
 
## Project structure
 
```
backend/
  controller/     auth and lesson handlers
  middleware/     JWT check and daily AI limit
  models/         User and Lesson schemas
  routes/         auth and lesson routes
  server.js       app entry and Gemini route
client/
  src/components/ lesson planner, flashcards, layout
  src/pages/      login, signup, lesson details, topics
  src/context/    auth state
```
 
## Known limitations and next steps
 
- The API base URL is currently set directly in the client code. Moving it to an environment variable is needed for deployment.
- Reminders are checked when the app loads, so they appear only when the app is opened on that day. Push notifications through a service worker would remove that limit.
- The JSON in the model response is extracted and parsed on the client. Using Gemini's structured output mode with a response schema would make this more reliable.
