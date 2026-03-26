💎 Premium Solana Faucet Bot
A personal Telegram bot that runs a task-based Solana faucet — users complete tasks to earn SOL, then claim it directly to their Solana wallet. Built with Node.js, Telegram Bot API, Solana Web3.js, and Firebase Firestore.
✨ What It Does
Task system — admins create tasks with SOL rewards; users complete them to earn
SOL payouts — users claim earned SOL by providing their Solana wallet address; the bot signs and submits the transaction on-chain
Anti-spam protection — 1-second cooldown between task verify clicks; repeated fast clicks result in a balance reset
Admin dashboard — inline keyboard panel for managing tasks, broadcasting messages, and resetting balances
Admin notifications — admins receive a message every time a user completes a task
Statistics — both public and admin-level views of total users, SOL distributed, and tasks completed
Webhook-based — runs on Express with a Telegram webhook endpoint
🏗️ Project Structure
├── index.js          # All bot logic, Solana ops, and Express server in one file
├── .env              # Environment config (not committed)
└── package.json
⚙️ Environment Variables
Create a .env file in the project root:
# Telegram
TELEGRAM_BOT_TOKEN=your_bot_token

# Solana
SOLANA_RPC_URL=https://api.devnet.solana.com
FAUCET_PRIVATE_KEY=your_bs58_encoded_private_key
DRIP_AMOUNT=1000000000
COOLDOWN_HOURS=1

# Server
PORT=3000
RENDER_URL=https://your-app.onrender.com

# Firebase
FIREBASE_TYPE=service_account
FIREBASE_PROJECT_ID=your_project_id
FIREBASE_PRIVATE_KEY_ID=your_key_id
FIREBASE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n"
FIREBASE_CLIENT_EMAIL=your_client_email
FIREBASE_CLIENT_ID=your_client_id
FIREBASE_AUTH_URI=https://accounts.google.com/o/oauth2/auth
FIREBASE_TOKEN_URI=https://oauth2.googleapis.com/token
FIREBASE_AUTH_PROVIDER_CERT_URL=https://www.googleapis.com/oauth2/v1/certs
FIREBASE_CLIENT_CERT_URL=your_cert_url
FIREBASE_UNIVERSE_DOMAIN=googleapis.com
⚠️ FAUCET_PRIVATE_KEY must be a Base58-encoded Solana private key. FIREBASE_PRIVATE_KEY must preserve literal \n characters — the app handles replacement automatically.
🚀 Getting Started
1. Install dependencies
npm install
2. Set up Firebase
Create a Firebase project and enable Firestore
Generate a service account key and copy the values into your .env
3. Fund your faucet wallet
The bot sends real SOL from FAUCET_PRIVATE_KEY. For devnet, airdrop SOL via:
solana airdrop 2 <your_faucet_address> --url devnet
4. Run the server
node index.js
The bot will set its webhook automatically to RENDER_URL/bot<token>.
📡 HTTP Endpoints
Method
Path
Description
POST
/bot<token>
Telegram webhook receiver
GET
/
Returns bot status and timestamp
💬 User Commands
Command
Description
/start
Welcome message with balance and menu
/claim <address>
Claims earned SOL to the provided Solana wallet address
/help
Shows available commands
👑 Admin Commands
Admin access is controlled by hardcoded numeric Telegram user IDs in the adminIds array.
Command
Description
/admin
Opens the admin panel
/add_task <link> <description> <reward>
Adds a new task with a SOL reward
/broadcast <message>
Sends a message to all registered users
/manage_tasks
Lists tasks with delete buttons
/stats
Shows detailed system statistics
/admin_reset_all_balances <amount>
Sets all user balances to a specific SOL value
/add_task example:
/add_task https://t.me/mychannel Join our Telegram channel 5
🎯 Task Flow
Admin adds a task with a link, description, and SOL reward
Users browse tasks from the Complete Tasks menu
User clicks Verify & Earn SOL — a 7-second simulated verification runs
On success, the SOL reward is added to the user's in-bot balance
Admins receive a notification of the completion
User runs /claim <wallet_address> to withdraw to their Solana wallet
🛡️ Anti-Spam
If a user clicks Verify twice within 1 second, their balance is reset to 0 and they receive a warning. The cooldown window is controlled by ACTION_COOLDOWN_MS (default: 1000ms).
The claim function also enforces a per-user cooldown (default: 1 hour, set via COOLDOWN_HOURS) between withdrawals.
🗄️ Firestore Collections
Collection
Contents
users
One document per user — balance, lastClaim, totalEarned, tasksCompleted, username
tasks
One document per task — name, link, description, reward, deleted flag
userTasks
One document per userId_taskId pair — tracks which tasks each user has completed
Tasks are soft-deleted (marked deleted: true) rather than removed from Firestore, so completion records remain intact.
🔑 Admin Setup
Open index.js and update the adminIds array with your Telegram numeric user IDs:
this.adminIds = [123456789, 987654321];
To find your Telegram ID, message @userinfobot.
🧰 Tech Stack
Layer
Technology
Runtime
Node.js
Telegram
node-telegram-bot-api
Blockchain
@solana/web3.js (Devnet by default)
Database
Firebase Firestore
HTTP Server
Express
Config
dotenv
📝 Notes
The bot defaults to Solana Devnet. To use Mainnet, change SOLANA_RPC_URL and ensure the faucet wallet has real SOL
Task verification is simulated with a 7-second delay — no on-chain check is performed
The in-memory userLastTaskAction map resets on server restart, which clears anti-spam state
This is a personal project — there are no authentication guards on the admin commands beyond the adminIds check
