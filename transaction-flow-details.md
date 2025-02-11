# ONDC Transaction Flow Details

## 1. Discovery Phase

### Search Flow
* **Buyer App → Gateway**: `/search`
  - Purpose: Find sellers and items
  - Key fields: 
    - Search parameters
    - Location details
    - Intent object
    
* **Gateway → Seller App**: `/search` (forwarded)
  - Purpose: Match sellers with search criteria
  - Added context:
    - BAP details
    - Transaction context
    
* **Seller App → Gateway**: `/on_search`
  - Response includes:
    - Catalog details
    - Provider information
    - Item details
    - Prices
    - Availability

## 2. Order Building Phase

### Select Flow
* **Buyer App → Gateway**: `/select`
  - Purpose: Select specific items
  - Includes:
    - Item IDs
    - Quantities
    - Provider details
    
* **Seller App → Gateway**: `/on_select`
  - Returns:
    - Quote with breakup
    - Item availability
    - Fulfillment options
    - Terms & conditions

### Init Flow
* **Buyer App → Gateway**: `/init`
  - Purpose: Initialize order
  - Contains:
    - Delivery address
    - Billing details
    - Selected items
    
* **Seller App → Gateway**: `/on_init`
  - Returns:
    - Draft order
    - Payment terms
    - Fulfillment details
    - Billing acknowledgment

### Confirm Flow
* **Buyer App → Gateway**: `/confirm`
  - Purpose: Finalize order
  - Includes:
    - Payment details
    - Final quantities
    - Delivery preferences
    
* **Seller App → Gateway**: `/on_confirm`
  - Returns:
    - Order confirmation
    - Order ID
    - Fulfillment details
    - Payment acknowledgment

## 3. Fulfillment Phase

### Status Tracking
* **Buyer App → Gateway**: `/status`
  - Purpose: Check order status
  - When to use:
    - Regular status checks
    - After updates
    - Before/after payments
    
* **Seller App → Gateway**: `/on_status`
  - Returns:
    - Current order state
    - Fulfillment status
    - Payment status
    - Any issues/blockers

### Tracking Flow
* **Buyer App → Gateway**: `/track`
  - Purpose: Track fulfillment
  - Used for:
    - Delivery tracking
    - Pickup status
    - Return tracking
    
* **Seller App → Gateway**: `/on_track`
  - Provides:
    - Real-time location
    - Delivery estimates
    - Agent details
    - Status updates

## 4. Order Management Phase

### Update Flow
* **Either Party → Gateway**: `/update`
  - Purpose: Modify order
  - Can update:
    - Quantities
    - Delivery details
    - Billing information
    - Fulfillment preferences
    
* **Seller App → Gateway**: `/on_update`
  - Returns:
    - Updated order state
    - Modified terms
    - New pricing if applicable
    - Updated timelines

### Cancel Flow
* **Either Party → Gateway**: `/cancel`
  - Purpose: Cancel order
  - Requires:
    - Cancellation reason
    - Order ID
    - Cancel terms acceptance
    
* **Seller App → Gateway**: `/on_cancel`
  - Returns:
    - Cancellation confirmation
    - Refund details
    - Updated order state
    - Settlement terms

## Key Points

1. **Context Persistence**
   - All APIs require context object
   - Transaction ID remains constant
   - Message IDs are unique per call

2. **State Management**
   - Each callback updates order state
   - States are sequential and logged
   - Both apps maintain state copies

3. **Error Handling**
   - Each call has error callbacks
   - Retry mechanisms exist
   - Timeout handling is defined

4. **Settlement Flows**
   - Payment capture points
   - Settlement triggers
   - Refund workflows

5. **Validation Rules**
   - Schema validation at each step
   - Business rule validation
   - State transition validation
