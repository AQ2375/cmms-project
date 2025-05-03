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

