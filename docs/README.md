<h1 align="center">
  <img src="https://raw.githubusercontent.com/gordonfn/api/main/docs/images/datastream.svg?sanitize=true" alt="DataStream Logo" width="400">
  <br/>
  DataStream Public API
  <br/>
  <br/>
</h1>
<p align="center">
  <a href="https://docs.google.com/forms/d/1SjPVeblz2QFaghpiBZPZKOVNKXgw5UMnAtJLJS1tQYI">Request an API Key</a>
</p>

Our public API uses the ISO/IEC 20802-2 Standard known as [OData JSON Format v4](https://odata.org).

## Attribution/Citation
Thank you ahead of time for using this data responsibly and providing the appropriate citations when necessary when being presented to external parties. These citations must be accompanied by a link to the DOI (https://doi.org/{value}). The licence, citation, and DOI can be retrieved from the `/Metadata` endpoint.

## Modules
We have built modules to wrap around our API to make it easier to use.
- [`R`](https://github.com/datastreamapp/datastreamr)
- [`Python`](https://github.com/datastreamapp/datastream-py)
- [`JavaScript`](https://github.com/datastreamapp/datastreamjs)
- [`Shell`](https://github.com/datastreamapp/datastreamsh)

For those building their own implementation, here are some key things to keep in mind:
- Querystring parameters must be URL encoded. All languages should have a function to do this.
- Requests to Observations/Records that you expect a large amount (>1M rows) of data from should be partitioned. We recommend by Monitoring locations and/or activity start year. There is a technical database reason for this that you're welcome to ask us about.
- Each request partition should be paginated over using the `Link` header or `@odata.nextLink` within the body of the response.
- Rate limit yourself (2 reqs/sec) and don't make requests in parallel. This will ensure you don't get `429 Too Many Requests` error response.
- Use HTTP/3

## Endpoints
You can test out your script by prefixing `https://api.qa.datastream.org/v1/odata/v4` to the endpoints.
When you're ready to pull data from the production system you can use: `https://api.datastream.org/v1/odata/v4`.
For browser requests all you need to do is let us know your domain name and we can add it to the CORS whitelist, only GET requests are supported. All other application should store the API Key in the header `x-api-key`.

Remember that your API key is for your use only. Please do not share your API key. If it does become public, please let us know, we can give you a new one.

- **GET /Metadata**
  - Retrieves the dataset-level metadata for the datasets that meet your query criteria.   
  - Select By: `DOI`, `Version`, `DatasetName`, `DataStewardEmail`, `DataCollectionOrganization`, `DataUploadOrganization`, `ProgressCode`, `MaintenanceFrequencyCode`, `Abstract`, `DataCollectionInformation`, `DataProcessing`, `FundingSources`, `DataSourceURL`, `OtherDataSources`, `Citation`,   `Licence`, `Disclaimer`, `TopicCategoryCode`, `Keywords`, `CreateTimestamp`, `PublishedTimestamp`
  - Filter By: `DOI`, `LocationId`, `ActivityMediaName`, `ActivityGroupType`, `CharacteristicName`, `MonitoringLocationType`, `ActivityStartYear`, `RegionId`, `LatitudeNormalized`\*, `LongitudeNormalized`\*, `DatasetName`, `CreateTimestamp`

- **GET /Locations**
  - Retrieves the location information that meets your query criteria.
  - Select By: `Id`, `DOI`, `ID` (Maps to `MonitoringLocationID` internally), `Name`, `Latitude`, `Longitude`, `HorizontalCoordinateReferenceSystem`, `HorizontalAccuracyMeasure`, `HorizontalAccuracyUnit`, `VerticalMeasure`, `VerticalUnit`, `MonitoringLocationType`, `LatitudeNormalized`\*, `LongitudeNormalized`\*, `HorizontalCoordinateReferenceSystemNormalized`\*
  - Filter By: `DOI`, `LocationId`, `ActivityMediaName`, `ActivityGroupType`, `CharacteristicName`, `MonitoringLocationType`, `ActivityStartYear`, `RegionId`, `LatitudeNormalized`\*, `LongitudeNormalized`\*, `Name`

- **GET /Observations**
  - Retrieves the observations that meet your query criteria. 
  - Select By: `Id`,`LocationId`,  `DOI`, `ActivityType`, `ActivityMediaName`, `ActivityStartDate`, `ActivityStartTime`, `ActivityStartTimeZone`, `ActivityEndDate`, `ActivityEndTime`, `ActivityEndTimeZone`, `ActivityDepthHeightMeasure`, `ActivityDepthHeightUnit`, `SampleCollectionEquipmentName`, `CharacteristicName`, `MethodSpeciation`, `ResultSampleFraction`, `ResultValue`, `ResultUnit`, `ResultValueType`, `ResultDetectionCondition`, `ResultDetectionQuantitationLimitUnit`, `ResultDetectionQuantitationLimitMeasure`, `ResultDetectionQuantitationLimitType`, `ResultStatusID`, `ResultComment`, `ResultAnalyticalMethodID`, `ResultAnalyticalMethodContext`, `ResultAnalyticalMethodName`, `AnalysisStartDate`, `AnalysisStartTime`, `AnalysisStartTimeZone`, `LaboratoryName`, `LaboratorySampleID`
  - Filter By: `DOI`, `LocationId`, `ActivityMediaName`, `ActivityGroupType`, `CharacteristicName`, `MonitoringLocationType`, `ActivityStartYear`, `RegionId`, `LatitudeNormalized`\*, `LongitudeNormalized`\*

 \* Normalized coordinates are in `WGS84` projection.
 
- **GET /Records**
  - Retrieves the data in the DataStream schema format with all columns that meet your query criteria.
  - Only intended for streaming data directly into a CSV file. For all other use cases, we recommend using `/Observations` endpoint.
  - Select By: `Id`, `DOI`, `DatasetName`, `MonitoringLocationID`, `MonitoringLocationName`, `MonitoringLocationLatitude`, `MonitoringLocationLongitude`, `MonitoringLocationHorizontalCoordinateReferenceSystem`, `MonitoringLocationHorizontalAccuracyMeasure`, `MonitoringLocationHorizontalAccuracyUnit`, `MonitoringLocationVerticalMeasure`, `MonitoringLocationVerticalUnit`, `MonitoringLocationType`, `ActivityType`, `ActivityMediaName`, `ActivityStartDate`, `ActivityStartTime`, `ActivityStartTimeZone`, `ActivityEndDate`, `ActivityEndTime`, `ActivityEndTimeZone`, `ActivityDepthHeightMeasure`, `ActivityDepthHeightUnit`, `SampleCollectionEquipmentName`, `CharacteristicName`, `MethodSpeciation`, `ResultSampleFraction`, `ResultValue`, `ResultUnit`, `ResultValueType`, `ResultDetectionCondition`, `ResultDetectionQuantitationLimitMeasure`, `ResultDetectionQuantitationLimitUnit`, `ResultDetectionQuantitationLimitType`, `ResultStatusID`, `ResultComment`, `ResultAnalyticalMethodID`, `ResultAnalyticalMethodContext`, `ResultAnalyticalMethodName`, `AnalysisStartDate`, `AnalysisStartTime`, `AnalysisStartTimeZone`, `LaboratoryName`, `LaboratorySampleID`
  - Filter By: `DOI`, `LocationId`, `ActivityMediaName`, `ActivityGroupType`, `CharacteristicName`, `MonitoringLocationType`, `ActivityStartYear`, `RegionId`


 
### Body Object
```json
{
  "@data.context": "odata/v4/Records/$links/Metadata(Id=@DatasetId)"
  "value":[
      ...
  ]
}
```

### URL Parameters
OData accepts certain query parameters. The ones supported by this API are:
- **$select**
  - Fields to be selected are entered comma delimited.
  - Example: `$select=DatasetName,Abstract`
  - Default: All columns available.
- **$filter**
  - Available operators: `in`, `eq`, `lt`, `gt`, `lte`, `gte`, `ne`
  - Grouping: `$filter=CharacteristicName eq 'Dissolved oxygen saturation'` or `$filter=DOI eq '10.25976/{suffix}'` where `{suffix}` is replaced with a value.
  - Temporal: `$filter=CreateTimestamp gt '2020-03-23' and CreateTimestamp lt '2020-03-25'`
  - Spatial: `$filter=RegionId eq 'hub.atlantic'`
    - RegionId Values (these values are subject to change):
      - Partner Hubs: `hub.{atlantic,greatlakes,lakewinnipeg,mackenzie,pacific}`
      - Countries: `admin.2.{ca}`
      - Provinces/Territories/States: `admin.4.ca.{ab,bc,mb,nb,nl,ns,nt,nu,on,pe,qc,sk,yt}`
    - Bounding box `$filter=LongitudeNormalized gt '-102.01' and LongitudeNormalized lt '-88.99' and LatitudeNormalized gt '49' and LatitudeNormalized lt '60'`
  <!-- - Functions: `$filter=contains(DOI, 'xxxx')`, `$filter=startwith(DOI, 'xxxx')`, `$filter=endswith(DOI, 'xxxx-xxxx')` -->
- **$top**
  - Maximum: 10000
  - Example: `$top=10`
- **$skiptoken**
  - Return the next items after the skipped token
  - Example: `$skiptoken=Id:1234`
  - For pagination, use `Link` header or `@odata.nextLink` in the JSON response body
- **$count**
  - Return only the count for the request. When the value is large enough it becomes an estimate (~0.0005% accurate)
  - Example: `$count=true`
  - Default: `false`
  - *Note: Unlike raw data retrieval, `$count=true` is not paginated and will not automatically iterate over locations. This can result in 408 or 413 error codes for large queries. Lowering `$top` will not resolve these errors. Instead, add narrower filters (e.g., LocationId, CharacteristicName, ActivityStartYear)*
<!--
- **$orderby**
  - Fields to order by are entered comma delimited.
  - Example: `$orderby=DatasetName,CreateTimestamp`
- **$skip**
  - Example: `$skip=10`
-->

When building an integration with any API, it's important to encode all query string parameters.

### Performance Tips
- Using `$select` to request only the parameters you need will decrease the amount of data needed to be transfer.
<!--
- Using large `$skip` values can be slow (it's a database thing), slicing your data by `GeometryId` and/or `CharacteristicName` will help prevent this.
- Don't use `$orderby` unless you plan to pull a smaller number of results.
-->

## Examples

### Metadata

#### Get all datasets in `Mackenzie` and `Lake Winnipeg` DataStream hubs
```bash
curl -G -H 'x-api-key: PRIVATE-API-KEY' \
     https://api.datastream.org/v1/odata/v4/Metadata \
     --data-urlencode "\$select=DOI,DatasetName,Licence,Citation,Version" \
     --data-urlencode "\$filter=RegionId in ('hub.mackenzie', 'hub.lakewinnipeg')"
```

#### Get the citation and licence for a dataset:
```bash
curl -G -H 'x-api-key: PRIVATE-API-KEY' \
     https://api.datastream.org/v1/odata/v4/Metadata \
     --data-urlencode "\$select=DOI,DatasetName,Licence,Citation,Version" \
     --data-urlencode "\$filter=endswith(DOI, 'xxxx-xxxx')" \
```

### Locations

#### Get Locations from a dataset
```bash
curl -G -H 'x-api-key: PRIVATE-API-KEY' \
     https://api.datastream.org/v1/odata/v4/Locations \
     --data-urlencode "\$filter=DOI eq '10.25976/xxxx-xx00'"
```

#### Get Locations from multiple datasets
```bash
curl -G -H 'x-api-key: PRIVATE-API-KEY' \
     https://api.datastream.org/v1/odata/v4/Locations \
     --data-urlencode "\$filter=DOI in ('10.25976/xxxx-xx00', '10.25976/xxxx-xx00', '10.25976/xxxx-xx00')"
```

### Observations

#### Get Temperature and pH observations from multiple datasets
```bash
curl -G -H 'x-api-key: PRIVATE-API-KEY' \
     https://api.datastream.org/v1/odata/v4/Observations \
     --data-urlencode "\$filter=DOI in ('10.25976/xxxx-xx00', '10.25976/xxxx-xx00', '10.25976/xxxx-xx00') and CharacteristicName in ('Temperature, water', 'pH')"
```

#### Get all `pH` observations in `Alberta`:
```bash
curl -G -H 'x-api-key: PRIVATE-API-KEY' \
     https://api.datastream.org/v1/odata/v4/Observations \
     --data-urlencode "\$filter=CharacteristicName eq 'pH' and RegionId eq 'admin.4.ca.ab'"
```

## Response Format
```json
{
  "value": [{
    "Id": "UUID",
    ...
  }],
  "@odata.nextLink": "https://api.datastream.org/v1/odata/v4/Observations?$skiptoken=Id:99999&$top=1000"
}
```

## Errors
### 408 or 504 Timeout
This means your request was too complicated and was unable to complete within 30sec. Lowering `$top` and/or adding in narrower filtering should resolve this issue.

### 413 Payload Too Large
This means your request result was too large. Lowering `$top` or only requesting the values you need should resolve this issue.

### 429 Too Many Requests
This status code indicates that the client has sent too many requests in a given amount of time. To resolve this issue, rate limit yourself (2 reqs/sec) and don't make requests in parallel.

## Disclaimer
We are currently in a Beta, changes will happen. We will do our best effort to keep you informed of any breaking changes.

<div align="center">
  <a href="http://gordonfoundation.ca"><img src="https://raw.githubusercontent.com/gordonfn/api/main/docs/images/the-gordon-foundation.svg?sanitize=true" alt="The Gordon Foundation Logo" width="200"></a>
</div>
