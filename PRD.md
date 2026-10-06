# AI Bot Studio Frontend: Product Requirements Document (reverse-engineered)

This spec was reconstructed from the shipped front-end code, not written before it. It describes what the app does today. Where something is inferred from names or structure, that is said. The server is covered in [AI-Bot-Studio-Backend](https://github.com/jsoftsol/AI-Bot-Studio-Backend) and the visitor-facing chat in [AI-Bot-Studio-Public](https://github.com/jsoftsol/AI-Bot-Studio-Public). Credentials, hostnames and security internals are left out on purpose.

## 1. Problem statement

A business wants a chat bot that answers from its own material, and it wants to set that up without writing code. The setup has many parts: instructions, knowledge sources, appearance, forms for collecting leads, and a connection to each messaging platform.

This app is where that setup happens. It turns the backend's capabilities into forms, previews and lists a non-technical user can work through.

## 2. Target users

| User | What they need |
|---|---|
| Business owner | Build a bot, train it on their content, publish it, read the leads |
| Marketer or support lead | Style the bot, add quick answers and a call-to-action, connect Freshdesk or a calendar |
| Social media manager | Connect Instagram or Messenger and set comment and message rules |
| Agency | Manage many clients' bots from one login |

## 3. Scope

### 3.1 Implemented

- Sign-in, password reset, password change
- Dashboard with stats, charts and a world map
- Bot library: create, clone with options, edit, delete, groups, tags, custom domain and URL slug
- Bot builder with these sections:
  - Settings, quick buttons and assistant and model choice
  - Instructions with an instruction generator
  - Knowledge: files, question and answer pairs, text, web links
  - Branding for the chat bubble and the chat page
  - Welcome gate, call-to-action, recommendations, appointment and incentive options
  - Connections: Calendly, Freshdesk, Square
  - Channels: WhatsApp, Messenger, Instagram, Gmail
  - Embed code
- Live bubble and page previews
- Training center: website crawl with summaries, file upload and tagging, linked bots, content from connected platforms
- Training progress shown from server events
- Instagram and Messenger automations: keywords, posts, comment and message setup, templates, tags, catch-all and state switches
- Assistants library, expert creator and edit page
- Leads, tags, domains: list, add, update, delete
- OpenAI key management and the user's own API token
- Agency clients: add, edit, profile image, log in as client, log out of client
- Video tutorials and onboarding, served by an outside service

### 3.2 Scaffolded or partial

- **Voice.** Enable switch and voice model work. The main setup and script tabs and the goal selector are behind a development flag, and the goal selector is empty.
- **Library wizard.** An alternate data source flow (files, Notion, Q and A, text, website) exists, but its route conflicts with the main library route.
- **Two-factor screens.** Template demo only.
- **Global Control service.** An empty class.
- **White-label build.** A second build mode and a menu flag exist for a branded variant, but one of its build scripts calls the wrong target.
- **Registration.** The route is commented out.

### 3.3 Out of scope

- Billing and subscriptions
- Role-based screens
- The chat that visitors use (separate project)
- Server logic
- Automated tests

## 4. Key flows

### 4.1 Sign in and enter the app

1. The user submits email and password.
2. The app stores the returned token and user, starts the socket connection and opens the dashboard.
3. Each route change checks that the user is loaded. A 403 from the server signs the user out.

### 4.2 Build and train a bot

1. The user creates a bot and fills in the settings, instructions and branding, watching the live preview.
2. The user adds knowledge: uploads files, pastes text, adds question and answer pairs or links.
3. The user starts training. The server works through the queue and the app shows progress from socket events.
4. The user copies the embed code or the public link.

### 4.3 Connect a messaging channel

1. The user opens Messaging Channels and starts a WhatsApp, Messenger, Instagram or Gmail connection.
2. The platform's own sign-in dialog opens, and the app passes the returned code to the server.
3. The bot is now reachable on that channel. Instagram and Messenger can also get automation rules.

### 4.4 Review leads

1. Visitors fill in the welcome gate or give details during a chat.
2. The user opens the leads page and filters or tags them.

### 4.5 Work inside a client account

1. The agency opens Multi Clients and chooses Log in as client.
2. The app stores a second token, adds it to every request and restarts the socket.
3. The agency edits the client's bots. Logging out of the client returns to the agency view.

## 5. Non-functional characteristics

- **Platform.** Browser single-page app, desktop-first, with a light, dark or system theme.
- **Realtime.** Training progress, notifications, assistant instruction generation and the preview chat arrive over Socket.IO.
- **Languages.** The i18n plugin is installed, but the screens are English.
- **Deployment.** Manual build and file copy. Separate targets for the app, dev and the white-label variant.
- **Testing.** None automated.

## 6. Risks

| Risk | Detail | Effect |
|---|---|---|
| Security review overdue | Session storage, how content is rendered and route access need an audit | Exposure of client and customer data |
| Template debris | Many unused template files and a second template tracked in git | Hard to tell what is live, slower builds |
| Hardcoded hosts | OAuth redirects for three channels point at fixed environments | Connections fail on other environments |
| Hidden features behind a flag | A development switch the user can toggle gates unfinished screens | Unfinished screens may be reached |
| No tests or CI | Manual build and copy | Regressions go unnoticed |
| Heavy bundle | Many libraries loaded in full | Slow first load |
| Duplicate and conflicting routes | Overlapping library routes, two paths per view | Unclear navigation |
| Single token model | Agency mode adds a second token to every request | Mistakes can write to the wrong account |

## 7. Open questions

- Should billing move into this app?
- Is voice meant to ship, and what is the goal selector for?
- Which template folders and demo pages can be deleted?
- Is the white-label build still in use?
