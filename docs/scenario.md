# 🛆 Usage Flow

## 1. Create a New User

User creation is part of the authentication system. Please refer to the login/register endpoints or contact support for onboarding.

---

## 2. Attach OKX API Key

Attach a new OKX API key to your user.

**Endpoint:**

```
POST /exchange_provider_user_api_key
```

**Request Body:**

```json
{
  "exchange_provider": "okx",
  "api_key": "your_okx_api_key",
  "secret_key": "your_okx_secret_key",
  "passphrase": "your_okx_passphrase"
}
```

**Delete API Key:**

```
DELETE /exchange_provider_user_api_key/{id}
```

Use this if you want to revoke or update your key.

---

## 3. Create a New Strategy Worker

Strategy workers are containers for your bots, powered by NFTs.

### 🔍 Get Available Strategy NFTs

```
GET /strategy_nfts
```

This returns a list of NFTs available to your company. Choose one to build a worker.

### 🛠️ Create Worker from NFT

```
POST /strategy/{strategy_id}/nft_worker
```

**Request Body:**

```json
{
  "exchange_provider_user_api_key_id": "your_key_id",
  "name": "My Worker",
  "settings": {
    // optional bot-specific config
  }
}
```

**Response:**
Returns the created worker and its `worker_id`.

---

## 4. View Bot Groups in a Worker

Get all bot groups associated with a strategy worker:

```
GET /strategy_nft_worker/{worker_id}/bot_groups
```

**Example Response:**

```json
[
  {
    "id": "bot_group_id",
    "name": "ETH Bot Group",
    "description": "Trades ETHUSDT with x5 leverage",
    "version_name": "v1.0",
    "version_description": "Initial setup"
  }
]
```

> You can use this for informational purposes or to control individual bot groups manually if needed.

---

## 5. Start the Worker

Start all bot groups under a strategy worker with a single request:

```
POST /strategy_nft_worker/{worker_id}/start
```

**Response:**

```json
{
  "status": "started"
}
```

## 🧠 Notes

* Strategy NFTs are only usable if owned by your company.
* Workers are isolated units allowing configuration per API key and strategy.
* Bot group versions exist, but auto-upgrade is handled internally and not currently exposed.
* Use WebSockets for real-time control and monitoring.
