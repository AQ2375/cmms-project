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
