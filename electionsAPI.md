> [!WARNING]
> This document is in a test/draft state and may not be accurate.

# CTCL Elections API Reference
This endpoint returns information about the elections covered by CTCL. Use the returns from this endpoint to populate the `electionId` parameter of your query to the [CTCL Voter Info endpoint](https://docs.google.com/document/d/e/2PACX-1vQsr98UusntEZx8PwXe9DSBi4XjXzkZ5yFbS2Rd8_IBgxX4xxTS2Psqmg4oMs5txwnfvFzRNwE-FRbW/pub). 
## Request
To request elections data, make a GET request to CTCL's elections endpoint:
| GET	`api.ballotinfo.org/elections` |
| --- | 

### Required parameters:
- **`key` (string)**: your API key. If you do not have an API key, contact hello@techandciviclife.org
### Optional parameters:
- **`address` (string)**: when provided, the API response will be limited to elections whose geographies overlap with the provided address. 
    - *Note: if  an address parameter is provided but is unable to be geocoded, an error is returned with code 400 and a reason of "parseError" as described in the [Google Civic Info API error reference here](https://developers.google.com/civic-information/docs/v2/errors).*

## Response
The following is a truncated response to a GET query made to `http://api.ballotinfo.org/elections?key=abcdefghijklmnop` (no address provided):
```json
{
	"kind": "civicinfo#electionsQueryResponse",
	"elections": [
		{
			"id": "ed1c4f40-ac85-44dc-a51a-372159e63da7",
			"name": "2022-09-06 Primaries",
			"electionDay": "2022-09-06",
			"ocdDivisionId": "ocd-division/country:us"
		},
		{
			"id": "7add402f-a0ad-4da5-a085-34877ad16155",
			"name": "2022-09-13 Primaries",
			"electionDay": "2022-09-13",
			"ocdDivisionId": "ocd-division/country:us"
		},
    { . . . additional elections not shown . . . }
	]
}
```

### kind
This returns "`civicinfo#electionsQueryResponse`" and serves as confirmation of the endpoint you are querying.

### elections
an array of `election` objects. Each `election` object contains the following properties:
- **id (uuid string)**: pass this as the electionId parameter in a voter info query
- **name**: The name of the election as recorded in CTCL's records. This should usually follow the format YYYY-MM-DD_election_type
- **electionDay**: The date on which the election is held in YYYY-MM-DD format
- **ocdDivisionId**: The highest level of OCDID for the election

If an `address` is provided as a query parameter, the returned elections will be limited to elections whose geographies overlap with the provided address. Otherwise, all elections are returned.

### normalizedInput
When an address parameter is provided with the request that is successfully geocoded, the human-readable address as understood by the endpoint is returned as the `normalizedInput` object. This object is described in the [Google Civic Info API reference here](https://developers.google.com/civic-information/docs/v2/elections/voterInfoQuery).

If the provided address cannot be geocoded, an error is returned with code 400 and a reason of "parseError" as described in the [Google Civic Info API error reference here](https://developers.google.com/civic-information/docs/v2/errors). 
