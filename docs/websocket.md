# WebSocket API

# 🔌 WebSocket Integration

Platform also provides real-time updates via WebSocket.

📄 [View WebSocket Message Documentation](https://gist.github.com/inthedark122/46c3250b9b78dd2ac8f0199545ec476f)

You can listen for:

* Worker status updates
* Bot execution logs
* Strategy metrics

# WebSocket Message Types

This document outlines the structure of WebSocket messages used for real-time updates within the system.

---

## WebSocket Message Types

The `WebSocket` type is a union of two primary message categories:

* **`WebsocketUserNotification`**: Messages intended for specific users.
* **`WebsocketSystem`**: System-wide messages, typically related to market data.

```typescript
export type WebSocket = WebsocketUserNotification | WebsocketSystem;
```

---

## User Notification Messages (`WebsocketUserNotification`)

These messages deliver real-time updates pertinent to a particular user, identified by `user_id`.

```typescript
export type WebsocketUserNotification = {
  event: "User";
  user_id: number;
  data:
    | WebsocketUserNotificationEvent // General notifications (ignored for documentation)
    | WebsocketUserPositionCreateEvent
    | WebsocketUserPositionUpdateEvent
    | WebsocketUserPositionOrderCreateEvent
    | WebsocketUserPositionOrderUpdateEvent
    | WebsocketUserNftEnergyUpdate;
};
```

### Event Details

#### `WebsocketUserNotificationEvent` (General Notification)

* **`event`**: `"Notification"`
* **`message`**: A string containing the notification message.
* **`params`**: An object holding additional parameters related to the notification.

#### Position-Related Events

These events relay updates about a user's trading positions and orders. They extend the base `BotPositionSchema` and `BotPositionOrderSchema` respectively.

* **`WebsocketUserPositionCreateEvent`**:
    * **`event`**: `"PositionCreate"`
    * **Description**: A new bot position has been created.
* **`WebsocketUserPositionUpdateEvent`**:
    * **`event`**: `"PositionUpdate"`
    * **Description**: An existing bot position has been updated.
* **`WebsocketUserPositionOrderCreateEvent`**:
    * **`event`**: `"PositionOrderCreate"`
    * **Description**: A new order within a bot position has been created.
* **`WebsocketUserPositionOrderUpdateEvent`**:
    * **`event`**: `"PositionOrderUpdate"`
    * **Description**: An existing order within a bot position has been updated.

#### `WebsocketUserNftEnergyUpdate` (NFT Energy Update)

* **`event`**: `"NftEnergyUpdate"`
* **`energy`**: The user's updated NFT energy level. To get the actual `f64` value, divide this number by `100_000_000`.
* **`tx`**: The transaction address associated with the energy update.

---

## System Messages (`WebsocketSystem`)

These messages provide system-wide information, primarily related to trading symbols.

```typescript
export type WebsocketSystem = {
  event: "Symbol";
  data: WebsocketSystemSymbolEvent;
};
```

### Event Details

#### `WebsocketSystemSymbolEvent` (Symbol Update)

* **`event`**: `"SymbolUpdate"`
* **`id`**: Unique identifier for the symbol.
* **`symbol`**: The trading pair symbol (e.g., "BTCUSDT", "BTC-USDT-SWAP").
* **`exchange_provider_id`**: Identifier for the exchange provider.
* **`can_trade`**: Indicates if the token is available for trading and present on the exchange.
* **`is_active`**: Signifies if the token is ready for trading. Tokens might be temporarily unavailable for new orders during system improvements or restarts.
* **`list_time`**: (Optional) Unix timestamp when the symbol was listed.
* **`exp_time`**: (Optional) Unix timestamp for the expiration time of the symbol.

---

## Shared Enums and Schemas

### Enums

#### `BotPositionStatus`

Defines the possible statuses of a bot trading position.

* `Created`
* `InProgress`
* `Finishing`
* `Completed`
* `Failed`

#### `BotPositionOrderStatus`

Defines the possible statuses of an order within a bot position.

* `New`
* `PartiallyFilled`
* `Filled`
* `Expired`
* `Canceled`

#### `OrderDirection`

Specifies the direction of an order or position.

* `BOTH`
* `LONG`
* `SHORT`

#### `BotPositionOrderType`

Defines the various types of orders.

* `Limit`
* `Market`
* `Stop`
* `StopMarket`
* `TakeProfit`
* `TakeProfitMarket`
* `TrailingStopMarket`

### Schemas

#### `BotPositionSchema`

Represents the detailed structure of a bot trading position.

* **`id`**: Unique identifier for the position.
* **`symbol_id`**: Identifier for the trading symbol.
* **`bot_id`**: Identifier for the bot managing the position.
* **`bot_group_id`**: (Optional) Identifier for the bot group.
* **`worker_id`**: (Optional) Identifier for the worker processing the position.
* **`side`**: The direction of the position (`LONG` or `SHORT`).
* **`qty`**: The quantity of the asset (Decimal string).
* **`entry_price`**: The entry price of the position (Decimal string).
* **`min_price`**: The minimum price reached by the position (Decimal string).
* **`max_price`**: The maximum price reached by the position (Decimal string).
* **`total_profit`**: The total profit generated (Decimal string).
* **`fee`**: The fees incurred (Decimal string).
* **`status`**: The current status of the position (`BotPositionStatus`).
* **`created_at`**: Unix timestamp of creation.
* **`update_at`**: Unix timestamp of the last update.

#### `BotPositionOrderSchema`

Represents the detailed structure of an order within a bot position.

* **`id`**: Unique identifier for the order.
* **`bot_position_id`**: Identifier of the parent bot position.
* **`price`**: The order price (Decimal string).
* **`stop_price`**: The stop price for stop orders (Decimal string).
* **`qty`**: The total quantity of the order (Decimal string).
* **`qty_filled`**: The quantity of the order that has been filled (Decimal string).
* **`side`**: The side of the order (`LONG` or `SHORT`).
* **`order_type`**: The type of order (`BotPositionOrderType`).
* **`status`**: The current status of the order (`BotPositionOrderStatus`).
* **`notes`**: (Ignored) Additional notes.
* **`update_at`**: Unix timestamp of the last update.
