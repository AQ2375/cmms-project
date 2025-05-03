# Functional Requirements Document (FRD)

## 1. Asset Registry

The Asset Registry is the “single source of truth” for every piece of equipment.  Below are the required fields:

| Field Name                  | Description                                                       | Data Type   | Required | Validation/Notes                                  |
| --------------------------- | ----------------------------------------------------------------- | ----------- | -------- | ------------------------------------------------- |
| Asset Code                  | System-generated unique identifier (Branch-Family-Category-Seq)    | String      | Yes      | Auto-generated based on code logic                 |
| Branch                      | Primary asset group (PLANT, MACHINERY, VEHICLE)                  | Enum        | Yes      | Dropdown list                                      |
| Family                      | Secondary group (e.g. Crane, Excavator, Loader)                  | Enum        | Yes      | Filtered by Branch                                 |
| Category                    | Sub-type (e.g. Crawler, Telescopic, Backhoe)                     | Enum        | Yes      | Filtered by Family                                 |
| Sub-Category                | Further classification (e.g. 50 T, 90 T under Crane)             | Enum        | No       | Optional                                           |
| Purchase Order No. (PO#)    | Procurement PO reference                                          | String      | Yes      | Lookup from PO master                              |
| PO Date                     | Date of purchase order                                            | Date        | Yes      | Must be ≤ today                                    |
| PO Value                    | Total contract value                                              | Decimal     | Yes      | ≥ 0                                                 |
| Supplier Name               | Vendor supplying the asset                                        | String      | Yes      | Free text or lookup                                |
| Warranty Terms              | Warranty duration and coverage summary                            | String      | No       | Free text                                          |
| Insurance Details           | Policy number & expiry date                                       | String      | No       | Free text                                          |
| Legalization Documents      | Registration/legal docs reference                                 | String      | No       | Free text                                          |
| Manufacturer Make & Model   | OEM make and model                                                | String      | Yes      | Free text                                          |
| Year of Manufacture         | Asset’s build year                                                | Integer     | Yes      | 4-digit year                                       |
| Serial Number               | OEM serial number                                                 | String      | Yes      | Unique per asset                                   |
| Current Hours/Odometer      | Operating hours or odometer reading                                | Integer     | Yes      | Updated via IoT or manual entry                    |
| Last Project Deployed       | Project name/location where asset was last used                   | String      | No       | Free text                                          |
| Last Deployment Date        | Date asset was last deployed                                      | Date        | No       | Must be ≤ today                                    |
| Disposal Date               | Date asset was retired/disposed                                   | Date        | No       | Blank until disposed                               |
| Disposal Method             | Sale, scrap, trade-in, donation                                   | Enum        | No       | Free list                                          |

## 2. Maintenance Management

This module handles Preventive, Predictive, and Reactive maintenance through Work Orders.

### 2.1 Work Order Fields

| Field Name             | Description                                                           | Data Type   | Required | Validation/Notes                                        |
| ---------------------- | --------------------------------------------------------------------- | ----------- | -------- | ------------------------------------------------------- |
| WO Number              | System-generated unique identifier                                    | String      | Yes      | Auto-generated (e.g. WO-20250503-0001)                  |
| Asset Code             | Link to the Asset Registry                                             | String      | Yes      | Must match an existing Asset Code                       |
| WO Type                | Preventive / Predictive / Reactive                                    | Enum        | Yes      | Dropdown                                               |
| Request Date           | Date the work order was raised                                        | Date        | Yes      | ≤ today                                                 |
| Scheduled Date         | Planned execution date                                                | Date        | Conditional | Must be ≥ Request Date (for Preventive)                 |
| Completion Date        | Actual completion date                                                | Date        | No       | ≥ Scheduled Date                                        |
| Priority               | Low / Medium / High / Critical                                        | Enum        | Yes      | Dropdown                                               |
| Description            | Detailed description of the maintenance task                           | Text        | Yes      |                                                       |
| Reported By            | User who raised the work order                                        | String      | Yes      | System user lookup                                     |
| Assigned To            | Technician or crew assigned                                           | String      | Yes      | Lookup from Technicians table                          |
| Estimated Hours        | Estimated labor hours                                                  | Decimal     | No       |                                                       |
| Actual Hours           | Actual labor hours spent                                              | Decimal     | No       |                                                       |
| Parts Required         | List of parts / materials                                              | Text        | No       | Can link to Parts Inventory                            |
| Status                 | Open / Scheduled / In Progress / Completed / Closed                   | Enum        | Yes      | Tracks WO lifecycle                                    |
| Verified By            | User who inspected & confirmed completion                              | String      | Conditional | Required when Status = Closed                           |
| Comments               | Free-text notes                                                        | Text        | No       |                                                       |

### 2.2 Preventive Scheduling

- **Trigger Types**:  
  - **Time-based** (e.g., every 500 hours or every 6 months)  
  - **Usage-based** (e.g., per odometer reading or machine hours)  
- **Schedule Definition**:  
  - Define on Asset record: `<Trigger Type>`, `<Interval Value>`, `<Interval Unit>`.  
  - System to auto-generate WOs when trigger threshold is reached.

### 2.3 Reactive Maintenance

- When a breakdown or issue is reported, a **Reactive** WO is created.  
- Must capture **Failure Code** and **Root Cause** (after closing).

### 2.4 Approvals & Notifications

- **Approval Flow**:  
  - If Estimated Hours > 8 or Priority = Critical → require Manager approval.  
- **Notifications**:  
  - Email/SMS to assigned technician on WO assignment.  
  - Reminder 24 hrs before Scheduled Date for Preventive WOs.
## 3. Parts & Inventory

This module manages spare parts, stock levels, and replenishment for all assets.

| Field Name           | Description                                                      | Data Type   | Required | Validation/Notes                                     |
| -------------------- | ---------------------------------------------------------------- | ----------- | -------- | ---------------------------------------------------- |
| Part ID              | Unique identifier                                                | String      | Yes      | Auto-generated or SKU                                |
| Part Name            | Descriptive name                                                 | String      | Yes      |                                                      |
| Part Category        | Category (e.g. Filters, Brakes, Hydraulics)                     | Enum        | Yes      | Dropdown                                              |
| Asset Type Code      | Link to compatible Asset Family/Category                          | String      | Yes      | Must match an existing Asset Type Code               |
| Vendor Reference     | Preferred supplier reference                                     | String      | No       | Lookup from Vendor master                            |
| Unit of Measure      | e.g. Piece, Litre, Kit                                          | Enum        | Yes      | Dropdown                                              |
| Location Code        | Warehouse or bin location                                        | String      | No       | Lookup from Location master                           |
| Current Stock        | On‐hand quantity                                                 | Integer     | Yes      | ≥ 0                                                   |
| Min Level            | Minimum reorder threshold                                        | Integer     | Yes      | ≥ 0                                                   |
| Max Level            | Maximum stock level                                              | Integer     | Yes      | ≥ Min Level                                           |
| Reorder Point        | Quantity at which system triggers a reorder                      | Integer     | Yes      | ≤ Max Level                                           |
| Reorder Quantity     | Default quantity to order                                        | Integer     | Yes      |                                                      |
| Lead Time (Days)     | Supplier lead time                                               | Integer     | No       |                                                      |
| Last Received Date   | Date of last delivery                                            | Date        | No       | ≤ today                                               |
| Warranty Terms       | Warranty coverage for the part                                   | String      | No       | Free text                                             |
| Remarks              | Additional notes                                                 | Text        | No       |                                                      |

### 3.1 Replenishment Rules

- Automatically generate **Purchase Requisitions** when `Current Stock ≤ Reorder Point`.  
- Respect `Min Level`/`Max Level` when calculating order quantity (e.g. order up to Max Level).

### 3.2 Stock Adjustments & Audits

- **Adjustments**: manual positive/negative adjustments with reason codes (theft, damage, overage).  
- **Audits**: periodic cycle counts by Location Code with variance reporting.

### 3.3 Notifications & Approvals

- **Notifications**: email to inventory manager when stock hits Reorder Point.  
- **Approval**: if `Reorder Quantity × Unit Cost > USD X,XXX`, require Manager approval.

