# CTCL Voter Info API Reference (BIP)
This endpoint returns information about contests and candidates in a particular election. Note that the CTCL API is largely a clone of the [Google Civic Info API's voterInfoQuery](https://developers.google.com/civic-information/docs/v2/elections/voterInfoQuery). 

This document explains:
- how to form a GET request to CTCL's Voter Info API
- where Google's Civic Info API differs from CTCL's Voter Info API;
- how the properties in the API response correspond to the data CTCL collects:
  - candidate collection fields for candidates are outlined in our [CTCL BIP Schema](https://docs.google.com/document/u/0/d/1CstQFkReE5ikYeb1wHQSgcp71Zo9cxTKrMBRct8QACY/edit),
  - collection fields for ballot measures (referenda) are outlined in our [CTCL Ballot Measure Schema](https://docs.google.com/document/u/0/d/1KNZEBUywTyKoIu9voGoSNy6kIlI5u_NFEbwSvMS56e4/edit)
## Request
To request candidate data, make a GET request to CTCL's voter info endpoint:
| GET	`api.ballotinfo.org/voterinfo` |
| --- |
> 
### Required parameters:
| parameter name (type) | description |
| --- | --- |
| `key` (string) | your API key. If you do not have an API key, contact hello@techandciviclife.org |
| `electionId` (uuid string) | the uuid string for the election you are looking up (use the `id` from the elections endpoint) |
| `address` (string) | the registered street address of the voter to look up |

**Example of a complete request:**
`http://api.ballotinfo.org/voterinfo?key=abc123def456ghi&address=303%20e%20wacker%2060601&electionId=c661578d-c328-4e0c-b8a7-9c69045073c4`

## Response
- **For the expected response for this method,** including data types, a brief description, and a list of enumerable options where appropriate (e.g. `contests[].level`), see the [Google Civic Information API documentation](https://developers.google.com/civic-information/docs/v2/elections/voterInfoQuery).
- *For additional context about how CTCL collects these fields,* see our collection schema docs:
  - [CTCL BIP Schema](https://docs.google.com/document/u/0/d/1CstQFkReE5ikYeb1wHQSgcp71Zo9cxTKrMBRct8QACY/edit) document for candidates
  - [CTCL Ballot Measure Schema](https://docs.google.com/document/u/0/d/1KNZEBUywTyKoIu9voGoSNy6kIlI5u_NFEbwSvMS56e4/edit) for ballot measures (contest type: "referendum")
### Omitted Properties
Note that the CTCL does **not** return the following properties that are included in the Google Civic Info API:
- `otherElections[]` (the CTCL API requires a specific electionId parameter with each request)
- `pollingLocations`
- `earlyVoteSites`
- `dropOffLocations`
- `state`
- `precinctId`
- `segments`
- `contests[].candidates[].orderOnBallot`

The CTCL API **does** return: 
[`kind`](https://developers.google.com/civic-information/docs/v2/elections/voterInfoQuery#kind), [election](https://developers.google.com/civic-information/docs/v2/elections/voterInfoQuery#election), [normalizedInput](https://developers.google.com/civic-information/docs/v2/elections/voterInfoQuery#normalizedInput), and [contests](https://developers.google.com/civic-information/docs/v2/elections/voterInfoQuery#contests). 
### Additional Properties
The CTCL Voter Info API returns one additional property not found in the Google Civic Info API: the `ctcl` object. Please note: the stability of this property beyond 2023 is not guaranteed. This object is returned with every candidate contest and includes the following key-value pairs:
| Property name (in contests[].ctcl) | Required | CTCL BIP Candidate Schema |
| --- | --- | --- |
| `voteVariation` | true | Vote Variation |
| `districtRollup` | false (this property is only included if "true" and is otherwise omitted) | N/A: this indicates that sub-district shapes are not available, and the jurisdiction OCDID is being returned as the contest OCDID |


### Candidate Contest Field Map
This section shows which property names in the API response correspond to which fields in the CTCL BIP Schema for candidate contests. 
- **The left column, "API Returns,"** shows the names of the properties as returned by the API, with links to the Google Civic Info API documentation. Use this to see the expected shape of the API response (noting the omissions listed above).
- **The right column, "CTCL Schema,"** shows the corresponding categories in the CTCL BIP Schema, with links to the appropriate section of the schema. Use this to see notes on how CTCL collects these fields.
Please note that this list only contains property names that are ambiguous/not a clear match. Fields like `contests[].candidates[].email` are not listed below (even though they are included in our API response), because the field name in the BIP Schema ("Email") is an obvious match.

| API Returns (link to Google API Docs) | CTCL BIP Candidate Schema |
| --- | --- |
| `contests[].office` | Office Name |
|`contests[].district.name`|Electoral District|
|`contests[].district.id`|Electoral District OCDID|
|`contests[].candidates[].name`|Candidate Name|
|`contests[].candidates[].party`|Candidate Party Registration|
|`contests[].candidates[].candidateUrl`|Website (Campaign)|


### Ballot Measure Field Map
Ballot measures are returned as contests from this same endpoint with a contest type "Referendum." This section contains a complete list of returned properties and their associated entry in the CTCL BIP Ballot Measure Schema.
| API Returns (link to Google API Docs) | CTCL BIP Ballot Measure Schema |
| --- | --- |
|`contests[].referendumTitle`|Title|
|`contests[].referendumSubtitle`|Subtitle|
|`contests[].referendumUrl`|Info URL|
|`contests[].referendumBrief`|Brief|
|`contests[].referendumText`|Full Text|
|`contests[].referendumProStatement`|Pro Statement|
|`contests[].referendumConStatement`|Con Statement|
|`contests[].referendumPassageThreshold`|Amount to Pass|
|`contests[].referendumEffectOfAbstain`|Abstain Effect|
|`contests[].referendumBallotResponses[]`|[Yes Response, No Response]|


