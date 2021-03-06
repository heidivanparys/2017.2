# Alternate Encoding for INSPIRE Data: GeoJSON

## Table of Contents

* [Introduction](#introduction)
* [Scope](#scope)
    * Use Cases
	* Themes
    * Technical Issues
	* Cross-cutting INSPIRE requirements
* [Normative References](#normative-references)
* [Terms and Definitions](#terms-and-definitions)
* [Schema Conversion Rules](#schema-conversion-rules)
    * Types
    * Properties
    * Associations
* [Instance Encoding Rules](#instance-encoding-rules)
    * Requirements and Recommendations
    * Mapping from Conceptual Model to GeoJSON Logical Model
    * Alternate Coordinate Reference Systems
* [Conformance Classes](#conformance-classes)
    * Simple Addresses
        * Model Transformation
        * Model Mapping
        * Examples
    * Simple Environmental Monitoring Facilities
        * Model Transformation
        * Model Mapping
        * Examples 

## Introduction

This section describes what this document contains and what the high-level objective of this encoding is.

GeoJSON is an open standard format designed for representing simple geographical features, along with their non-spatial attributes. It is based on JSON, the JavaScript Object Notation. The body of the specification deals with describing GeoJSON Geometry Objects. These may be points (which can be used for features like addresses and locations), line strings (e.g. for streets, highways, boundaries), polygons (countries, provinces, tracts of land), and multi-part collections of these types.

GeoJSON features need not represent entities of the physical world only; mobile routing and navigation apps, for example, might describe their service coverage using GeoJSON.

The GeoJSON format has originally been defined by an Internet working group of developers who needed a solution to encode geometries for use in web applications. It has since been formalised as an [IETF internet standard](https://tools.ietf.org/html/rfc7946). The IETF is the premier Internet standards organization.

A notable offspring of GeoJSON is [TopoJSON](https://github.com/topojson/topojson), an extension of GeoJSON that encodes geospatial topology and that provides smaller file sizes for polygons or other data sets where multiple features share geometries.

Within INSPIRE, this encoding represents an *alternative* encoding for data from several themes, with a focus on usability of the data in GIS desktop and web clients such as ArcMap, QGIS, OpenLayers, Leaflet, FME and hale studio. It can serve as an alternative encoding that can be used instead of the default encoding for simple data, where there is no information loss. In other cases, this GeoJSON encoding may serve as an *additional* encoding only.

This draft Alternative Encoding encompasses the INSPIRE themes Addresses (including GeographicalName properties) and Environmental Monitoring Facilities (including O&M properties).

## Scope

This sections describes the scope of the GeoJSON alternate encoding. GeoJSON is not equally well suited for all themes and use cases.

### Use Cases

This alternate encoding specifically addresses data usability in web and desktop client software, such as ArcMap, QGIS, Leaflet and OpenLayers. It optimizes usage of INSPIRE data for mapping and geoprocessing in such applications.

The encoding is also developed with the best practices for [Spatial data on the Web](https://www.w3.org/TR/sdw-bp/) and the [WFS 3.0 standard](https://github.com/opengeospatial/WFS_FES) in mind, for which it should provide a complementary format. 

### Coverage of INSPIRE Themes

This encoding covers the following themes:

* Annex I: Addresses
* Annex III: Environmental Monitoring Facilities

### Technical Issues

The encoding resolves specific technical issues that have been problematic when using the default encoding:

* Most GIS software cannot fully make use of non-smple attributes and nested structures for styling, processing and filtering;
* Support for multiple values per property is an issue in ArcGIS and other GIS tools;
* References to other features often cannot be resolved by GIS tools; Propertes of referenced features cannot be used in styling or for filtering ;
* Abstract geometry types for an object mean that a wide range of different geometries can be used;
* Mixed geometry types in a FeatureCollection are usually not supported;

The encoding does not cover:

* 3D geometries
* Coverage/Raster data

### INSPIRE Requirements for Encodings

The Implementing Rules on interoperability of spatial data sets and services (Commission Regulation (EU) No 1089/2010) lays down the following requirements for encodings:

> **Article 7 -- Encoding**
>
> 1. Every encoding rule used to encode spatial data shall conform to EN ISO 19118. In particular, it shall specify schema
> conversion rules for all spatial object types and all attributes and association roles and the output data structure used. 
> 2. Every encoding rule used to encode spatial data shall be made available.

D2.7 specifies more detailed requirements and recommendations for encodings. The following list lists the requirements from that document and shows which ones are also met in this alternate encoding:

> * Requirement 12: ... documents shall be required to be encoded using UTF-8 as character encoding.

D2.7 also contains a relevant recommendation:

* Recommendation 3: Encoding rules should be based on international, preferably open, standards.

## Normative References

This section contains references to standards documents and related resources.

* [GeoJSON - IETF RFC 7946](https://tools.ietf.org/html/rfc7946)
* [OGC Observations and Measurements - Simple Feature model & encodings (OMSF)](https://github.com/opengeospatial/omsf-profile)
* [OGC Observations and Measurements - Simple Feature GeoJSON Encoding (OMSF GeoJSON)](https://github.com/opengeospatial/omsf-profile/tree/master/omsf-json)
* [Data Specification - INSPIRE Addresses](https://inspire.ec.europa.eu/Themes/79/2892)
* [Data Specification - INSPIRE Environmental Monitoring Facilities](https://inspire.ec.europa.eu/Themes/120/2892)

## Terms and Definitions

Terms and Definitions can be found in the [Glossary](../glossary.md) document.

## Schema Conversion Rules

INSPIRE defines the conceptual model using UML, while the default encoding uses XML Schema to define the logical schema. There is no equivalent to such a logical schema language for JSON.  

NOTE At this point, [JSON Schema](https://json-schema.org/) is undergoing rapid development and cannot be considered stable enough.

As a consequence, the conversions required to go from the UML conceptual model to a model that can be directly encoded in simple GeoJSON, all model transformations are applied at the conceptual level of the UML model. 

All model transformation rules are applied in such a way that the resulting property names for valid XML element and type names, and are useable as property names in JSON.

### Types

#### General types 

Typenames remain as they are. All types that have the stereotype `<<featureType>>` are converted to GeoJSON objects. All other types are mapped to regular JSON objects, where properties of the type are directly added to the root object instead of being added to the `properties` subobject.

#### ISO 19107 - Geometry types

ISO 19107 defines a set of Geometry types, which need to be mapped to the types available in GeoJSON. Note that not all types can be mapped to GeoJSON; if an data set requires such a type, it cannot use this encoding as an alternative encoding.

| XML Schema datatype | GeoJSON datatype | Conversion Notes | 
| ------ | ----- | ----- |
| GM_Aggregate      | GeometryCollection | Limitations apply as to which types in the collection can be included. |
| GM_Curve          | LineString | In GML, Curves can also be nonlinear segments or arcs. GeoJSON only supports linear segments. |
| GM_MultiCurve     | MultiLineString | In GML, Curves can also be nonlinear segments or arcs. GeoJSON only supports linear segments. |
| GM_MultiPoint     | MultiPoint |  |
| GM_MultiPrimitive | not supported | `GM_MultiPrimitive` is an abstract type. |
| GM_MultiSurface   | Polygon |  |
| GM_Object         | not supported | `GM_Object` is an abstract type. |
| GM_Point          | Point |  |
| GM_PolyhedralSurface | not supported | At this point, this specification does not support 3D meshes. |
| GM_Primitive      | not supported | `GM_Primitive` is an abstract type. |
| GM_Ring           | Polygon | A `GM_Ring` is not intended to be used as a standalone geometry object. It is typically used to define an interior or exterior boundary in a Surface and is thus mapped to Polygon. |
| GM_Surface        | Polygon | A `GM_Surface` can have many SurfacePatches, it is thus mapped to MultiPolygon. |
| GM_Tin            | not supported | TODO A TIN without triangulation could be converted to a MultiPoint object. |
| GM_Triangle       | Polygon |  |

Where a class has one attribute with a geometry type, this attribute will be mapped to the `geometry` property in GeoJSON, while all other properties (attributes and association roles) will be mapped to properties inside the `properties`. A theme-specific profile has to define which geometry property is the "default" geometry that should be mapped to the `geometry` property in GeoJSON.

#### ISO 19108 - Temporal types

For types from ISO 19108 used in INSPIRE schemas, suitable mappings need to be found on a case-by-case basis. The default should be to use the [Simple Period](../model-transformation/SimplePeriod.md) substitution rule.

#### ISO 19115 - Metadata types

For types from ISO 19108 used in INSPIRE schemas, suitable mappings need to be found on a case-by-case basis. For `CI_Citation`, the [Simple Citation](../model-transformation/SimpleCitation.md) substitution rule can be applied.

#### Abstract Types and Inheritance

As `abstract` types cannot be encoded directly, the inheritance hierarchy is collapsed to the concrete types. Abstract types are then removed from the model. 

#### Union Types

A `union` represents a choice between multiple properties with different value types, such as a `ReferenceType` and a `BuildingType`. They can have a multiplicity other than exactly 1. The most common use case for union types in INSPIRE is to offer a choice between encoding an object in place or to encode it by reference.

For the purpose of creating GeoJSON, `union` types are replaced with the concrete type in the `union`, unless the [Property Composition to Association transformation rule](../model-transformation/PropertyCompositionToAssociation.md) is applied by the theme profile. For 

#### Enumerations and Code Lists

Enumerations and classes with the sterotype `<<Codelist>>` remain unchanged.

### Properties

Property names remain as they are.

* TODO: Should namespace prefixes be retained for the JSON property names?

### Mapping Property types from Conceptual Model to GeoJSON Logical Model

All property types are transformed to the simple types that JSON knows about: Number, String, Boolean and Object. The exact mapping from the UML model to the JSON datatypes is outlined in the following table:

| UML Model property type | JSON datatype | Conversion Notes | 
| ------ | ----- | ----- |
| CharacterString | string |  |
| LocalisedCharacterString | string | `LanguageCode` is added as a separate property. |
| Boolean | boolean |  |
| Integer | integer |  |
| Float | number |  |
| Double | number |  |
| DateTime | string | The string must follow the ISO 8601 format, including timezone information (e.g. `2008-10-31T15:07:38-05:00`). |
| Date | string | The string most follow the format `yyyy-mm-dd`. |
| Length | double | `uom` is added as a separate property. |
| Measure | double | `uom` is added as a separate property. |
| URI | string |  |
| URL | string |  |

Any other UML Model property type are to be mapped to `string`, with specific rules being defined on a case-by-case basis in each theme profile.

#### Properties with a `uom` attribute

The unit of measurement attribute (`uom`) on any property `x` has to be retained. It is transformed to a new property of the type with the name `x.uom`.

#### Arrays

Property types for properties with a cardinality greater than `1` and a simple property type (e.g. String, Integer, Float, ...) may use arrays of these simple types. More information is available in the [Extract Primitive Array](../model-transformation/ExtractPrimitiveArray.md) transformation rule.

#### Voidable

The stereotype `<<voidable>>` is not converted. If a property has no value, it may be dropped.

### Association Roles

Association roles are retained as they are, if they are not transformed using a profile-specific rule, such as [Inline associated or aggregated components using type names](../model-transformation/AssociatedComponentsHardType).

## Instance Encoding Rules

This section describes how the encoding is derived from the converted conceptual model, and describes which common rules have to be applied for this encoding.

### Common Rules

* `GEOJSON-REQ-01`: The character encoding of all data encoding in GeoJSON shall be UTF-8.
* `GEOJSON-REQ-02`: As per the requirements of the GeoJSON - IETF RFC 7946 specification, the default CRS for any data set delivered in this encoding shall be the World Geodetic System 1984 ([CRS 84](http://www.opengis.net/def/crs/OGC/1.3/CRS84)), unless there is prior arrangement.

NOTE As INSPIRE mandates the use of the European Terrestrial Reference System 1989  (ETRS89, see [Requirement 1](https://inspire.ec.europa.eu/reports/ImplementingRules/DataSpecifications/INSPIRE_Specification_CRS_v2.0.pdf)) for the areas within the geographical scope of ETRS89 and both CRS84 and ETRS89 use the GRS 80 ellipsoid (although with minor enhancements), we shall assume CRS 84 to be equivalent to ETRS89. If, for any dataset, this assumption would be problematic, then GeoJSON cannot serve as an alternative encoding for that dataset.

* `GEOJSON-REQ-03`: In the GeoJSON encoding, `nilReason` information shall not be maintained per feature, but rather in the dataset metadata. Properties that have `nil` values shall thus be ignored in the encoding.

NOTE If, for any dataset, there is specific `nilReason` information per feature, then GeoJSON cannot serve as an alternative encoding for that dataset

### Alternate Coordinate Reference System support

While the required Coordinate Reference System for any data encoded in GeoJSON is CRS84, a client may request delivery of a data set using a different projected reference system, as per the mechanism described in Requirement 8 in the [WFS 3.0 draft specification](https://github.com/opengeospatial/WFS_FES). 

* `GEOJSON-REC-01`: An INSPIRE Download service delivering data encoded in GeoJSON shall be able to deliver projected geometries if a client requests these explicitly, at least for the spatial reference systems documented in section 6.3. of the data specifications that fall within the scope of this encodign specification. When delivering data that is not in [CRS 84](http://www.opengis.net/def/crs/OGC/1.3/CRS84), the GeoJSON data should include the `crs` member as defined in the deprecated (Draft 6 of the GeoJSON specification)[http://wiki.geojson.org/GeoJSON_draft_version_6].

## Conformance Classes

This specification defines one conformance class per supported theme. 

Any conformance class in an encoding specification may  define a number of model transformation rules that need to be applied before the encoding. These transformations are documented in the [Model Transformation Rules](../model-transformations/TransformationRules.md) paper. They serve the purpose of adapting the conceptual model (UML) to better match the logical model of the target platform. In the context of this conformance class in the GeoJSON encoding, the described rules address the following issues:

* Multiple Geometries
* Nested Properties
* References to other elements and code lists
* Attributes such as `uom` and `nilReason`
* Arrays/Lists

### Simple Addresses (ADS)

The Simple Addresses encoding can be used as an *alternate encoding* for address data that fulfills the following requirements:

* It is sufficient to provide one `GeographicName` for all elements that use it 
* There is not more than 1 `AdminUnitName` address component per `AdministrativeUnitLevel`
* There is only a single default position per `Address` object

#### Model Transformation

This section describes which rules with which parameters are applied to the Addresses conceptual model before applying the general rules of this encoding:

1. Substitute all occurences of `GeographicName` with the Simple Geographic Name through Rule `MT005(separator: '.')`. 
2. Subsitute all attributes that have a property type with a Codelist Sterotype through a inline codelist reference using `MT008()`.
3. Inline all `addressComponents` through Rule `MT003(separator: '.', cardinality: { AdminUnitName: 6 })`, using the respective typenames to create unique property names. In addition, define that for `AdminUnitName`, six properties shall be created, one for each `AdministrativeUnitLevel`.
4. Flatten the Locator/Designator structure through application of `MT004(separator: '.', keyProperty: 'type')` (Flatten aggregated or associated components using codelist values).
5. Apply the General Flattening rule to simplify the remaining properties: `MT001(separator: '.')`

#### Model Mapping

The following table explains the mapping between the classes and properties of the original Addresses (AD) model to the Simplified Addresses (ADS) model.

| AD Name (condition) | AD Type | ADS Name | ADS Type |
| ------------------- | ------- | -------- | -------- |
| *Address** | AddressType | **SimpleAddress** | SimpleAddressType |
| ad:alternativeIdentifier | String | alternativeIdentifier | String |
| ad:beginLifespanVersion | DateTime | beginLifespanVersion | String |
| ad:endLifespanVersion | DateTime | endLifespanVersion | String |
| ad:building | ReferenceType | endLifespanVersion | String |
| ad:component (class = ThoroughfareName) | ReferenceType | component.ThoroughfareName | String |
| ad:component (class = PostalDescriptor) | ReferenceType | component.ThoroughfareName | String |
| ad:component (class = AddressAreaName) | ReferenceType | component.ThoroughfareName | String |
| ad:component (class = AdminUnitName, index = 0) | ReferenceType | component.AdminUnitName_1 | String |
| ad:component (class = AdminUnitName, index = 1) | ReferenceType | component.AdminUnitName_2 | String |
| ad:component (class = AdminUnitName, index = 2) | ReferenceType | component.AdminUnitName_3 | String |
| ad:component (class = AdminUnitName, index = 3) | ReferenceType | component.AdminUnitName_4 | String |
| ad:component (class = AdminUnitName, index = 4) | ReferenceType | component.AdminUnitName_5 | String |
| ad:component (class = AdminUnitName, index = 5) | ReferenceType | component.AdminUnitName_6 | String |
| gml:description | String | description | String |
| gml:id | ID | *added as `id` to the root object* | String |
| base:inspireId | IdentifierPropertyType | inspireId.localid | String |
|  |  | inspireId.namespace | String |
| ad:locator | AddressLocator | locator.designator.addressNumber | String |
|  |  | locator.designator.addressNumberExtension | String |
|  |  | locator.designator.addressNumber2ndExtension | String |
|  |  | locator.designator.buildingIdentifier | String |
|  |  | locator.designator.buildingIdentifierPrefix | String |
|  |  | locator.designator.cornerAddress1stIdentifier | String |
|  |  | locator.designator.cornerAddress2ndIdentifier | String |
|  |  | locator.designator.entranceDoorIdentifier | String |
|  |  | locator.designator.floorIdentifier | String |
|  |  | locator.designator.kilometrePoint | String |
|  |  | locator.designator.postalDeliveryIdentifier | String |
|  |  | locator.designator.staircaseIdentifier | String |
|  |  | locator.designator.unitIdentifier | String |
|  |  | locator.level | String |
|  |  | locator.level.href | String (URL) |
| gml:name | String | name | String |
| ad:parcel | ReferenceType | parcel | String |
| ad:parentAddress | ReferenceType | parentAddress | String |
| ad:position | GeographicPosition | *added as `geometry` to the root object* | GeoJSON Geometry Object |
|  |  | position.specification | String |
|  |  | position.specification.href | String (URL) |
|  |  | position.method | String |
|  |  | position.method.href | String (URL) |
|  |  | position.default | boolean |
| ad:status | ReferenceType | status | String |
|  |  | status.href | String (URL) |
| ad:validFrom | DateTime | validFrom | String |
| ad:validTo | DateTime | validTo | String |

#### Examples (Informative)

Example 1: One Address Feature with all components and locators inlined

```json
{  
   "type":"FeatureCollection",
   "features":[ 
        {
            "type": "Feature",
            "id": "Address_1",
            "geometry": {
                "type": "Point",
                "coordinates": [ 5.460372, 48.078589 ]
            },
            "properties": {
                "inspireId.localId": "Address_1",
                "inspireId.namespace": "https://https://github.com/INSPIRE-MIF/2017.2/GeoJSON/examples/ads/",
                "position.specification": "parcel",
                "position.specification.href": "http://inspire.ec.europa.eu/codelist/GeometrySpecificationValue/parcel",
                "position.method": "fromFeature",
                "position.method.href": "http://inspire.ec.europa.eu/codelist/GeometryMethodValue/fromFeature",
                "position.default": true,
                "locator.designator.addressNumber": "5",
                "locator.designator.addressNumberExtension": "A",
                "locator.level": "siteLevel",
                "locator.level.href": "http://inspire.ec.europa.eu/codelist/LocatorLevelValue/siteLevel",
                "component.ThoroughfareName": "Fraunhoferstraße",
                "component.PostalDescriptor": "64283",
                "component.AddressAreaName": "Innenstadt",
                "component.AdminUnitName_1": "Deutschland",
                "component.AdminUnitName_2": "Hessen",
                "component.AdminUnitName_3": "Darmstadt"
            }
        }
   ]
}
```

### Simple Environmental Monitoring Facilities (EMS)

Environmental Monitoring Facilities describe measuring stations, networks and programs, and makes use of Observations & Measurement (`OM`) data, in many cases using specific observations (`OMSO`).

The purpose of the `EMS` alternate encoding is to create a simple structure for the data directly related to the monitoring facility itself so that users can create simple maps with info on measurements station with the actual measures inlined. A typical feature will thus describe *one* monitoring facility with one measurement result for one given point in time. Since information about the point in time and the result are inlined, tools such as ArcGIS can use this information with the inbuilt time series visualisation tools as well.

An additional objective is to provide an efficient structure for the measurements made by a monitoring facility. This is achieved by building on the rules defined in the `OMSF GeoJSON` encoding.

#### Model Transformation

This section describes which rules with which parameters are applied to the Environmental Monitoring Facilities conceptual model before applying the general rules of this encoding:

1. Substitute all occurences of `LegalCitation` with the Simple Citation through Rule `MT007()`.
2. Apply `MT006()` to add optional external information about citations.
3. Substitute all attributes that have a property type with a Codelist Sterotype through a simple codelist reference using `MT008()`.
4. Substitute `OperationalActivityPeriod` with the Simple Period using `MT009()`.
5. Substitute all `OM` and `OMSO` model elements through the respective `OMSF` model elements.
6. Apply the `OMSF GeoJSON` model mapping to the `OMSO` model elements.
7. Apply the General Flattening rule to simplify the remaining properties: `MT001(separator: '.')`.

#### Examples (Informative)

Example 1: One Environmental Monitoring Facility with a single measurement result

```json
{  
   "type":"FeatureCollection",
   "features":[ 
        {
            "type": "Feature",
            "id": "EnvironmentalMonitoringFacility_1",
            "geometry": {
                "type": "Point",
                "coordinates": [ 24.96131, 60.20307 ]
            },
            "properties": {
                "description": "Water well from national BSS (Banque du Sous-Sol) Data database. Piezometer monitoring ground water level",
                "inspireId.localId": "EnvironmentalMonitoringFacility_1",
                "inspireId.namespace": "https://https://github.com/INSPIRE-MIF/2017.2/GeoJSON/examples/ems/",
                "identifier": "https://https://github.com/INSPIRE-MIF/2017.2/GeoJSON/examples/ems/EnvironmentalMonitoringFacility_1",
                "legalBackground.date": "",
                "legalBackground.link": "",
                "legalBackground.name": "",
                "legalBackground.level": "",
                "legalBackground.level.href": "",
                "legalBackground.type": "",
                "mediaMonitored": "water",
                "mediaMonitored.href": "http://inspire.ec.europa.eu/codelist/MediaValue/water",
                "mobile": false,
                "name": "Piézomètre de St-Rémy - 01",
                "observation.resultTime": "2017-08-17T12:11:20Z",
                "observation.result": 12.5,
                "observation.unitOfMeasureName": "Degree Celsius",
                "onlineResource": "http://fichebsseau.brgm.fr/bss_eau/fiche.jsf?code=06512X0037/STREMY",
                "operationalActivityPeriod.beginPosition": "1977-10-08T23:00:00Z",
                "operationalActivityPeriod.endPosition": "2014-10-14T06:00:00Z",
                "purpose": "Ground water level measurement",
                "purpose.href": "http://www.sandre.eaufrance.fr/?urn=urn:sandre:donnees:148::CdElement:2:::referentiel:3.1:xml",
                "resultAcquisitionSource": "in-situ",
                "resultAcquisitionSource.href": "http://inspire.ec.europa.eu/codelist/ResultAcquisitionSourceValue/inSitu/",
                "specialisedEMFType": "Piezometre",
                "specialisedEMFType.href": "http://www.sandre.eaufrance.fr/urn.php?urn=urn:sandre:dictionnaire:PTE::entite:Piezometre:ressource:2.1:::html"
            }
        }
   ]
}
```

Example 2: One Environmental Monitoring Facility with a reference to a `MeasureTimeseriesObservation`

```json
{  
   "type":"FeatureCollection",
   "features":[
       {
            "type": "Feature",
            "id": "EnvironmentalMonitoringFacility_1",
            "geometry": {
                "type": "Point",
                "coordinates": [ 24.96131, 60.20307 ]
            },
            "properties": {
                "description": "Water well from national BSS (Banque du Sous-Sol) Data database. Piezometer monitoring ground water level",
                "inspireId.localId": "EnvironmentalMonitoringFacility_1",
                "inspireId.namespace": "https://https://github.com/INSPIRE-MIF/2017.2/GeoJSON/examples/ems/",
                "identifier": "https://https://github.com/INSPIRE-MIF/2017.2/GeoJSON/examples/ems/EnvironmentalMonitoringFacility_1",
                "mediaMonitored": "water",
                "mediaMonitored.href": "http://inspire.ec.europa.eu/codelist/MediaValue/water",
                "name": "Piézomètre de St-Rémy - 01",
                "hasObservation.href": "#MeasureTimeseriesObservation_1",
                "onlineResource": "http://fichebsseau.brgm.fr/bss_eau/fiche.jsf?code=06512X0037/STREMY",
                "operationalActivityPeriod.beginPosition": "1977-10-08T23:00:00Z",
                "operationalActivityPeriod.endPosition": "2014-10-14T06:00:00Z",
                "purpose": "Ground water level measurement",
                "purpose.href": "http://www.sandre.eaufrance.fr/?urn=urn:sandre:donnees:148::CdElement:2:::referentiel:3.1:xml",
                "resultAcquisitionSource": "in-situ",
                "resultAcquisitionSource.href": "http://inspire.ec.europa.eu/codelist/ResultAcquisitionSourceValue/inSitu/",
                "specialisedEMFType": "Piezometre",
                "specialisedEMFType.href": "http://www.sandre.eaufrance.fr/urn.php?urn=urn:sandre:dictionnaire:PTE::entite:Piezometre:ressource:2.1:::html"
            }
        },
        {
            "type": "Feature",
            "id": "MeasureTimeseriesObservation_1",
            "geometry": {
                "type": "Point",
                "coordinates": [ 24.96131, 60.20307 ]
            },
            "properties": {
                "observationType": "MeasureTimeseriesObservation",
                "phenomenonTimeStart": "2017-08-17T12:00:00Z",
                "phenomenonTimeEnd": "2017-08-17T18:00:00Z",
                "resultTime": "2017-08-17T12:11:20Z",
                "usedProcedureName": "Meteorological surface observations",
                "usedProcedureReference": "http://xml.fmi.fi/process/met-surface-observations",
                "observedPropertyName": "air_temperature",
                "observedPropertyReference": "http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/",
                "samplingFeatureName": "Helsinki Kumpula weather observation station",
                "ultimateFeatureOfInterestName": "Helsinki Kumpula",
                "ultimateFeatureOfInterestReference": "http://sws.geonames.org/843429/about.rdf",
                "timeStep": [
                    "2017-08-17T12:00:00Z",
                    "2017-08-17T13:00:00Z",
                    "2017-08-17T14:00:00Z",
                    "2017-08-17T15:00:00Z",
                    "2017-08-17T16:00:00Z",
                    "2017-08-17T17:00:00Z",
                    "2017-08-17T18:00:00Z"
                ],
                "unitOfMeasureName": "Degree Celsius",
                "unitOfMeasureReference": "http://www.opengis.net/def/uom/UCUM/degC",
                "result": [12.5, 12.0, 11.0, 13.2, 13.5, 14.1, 14.1]
            }
        }
   ]
}
```