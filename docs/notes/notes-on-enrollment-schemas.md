# Enrollment Schemas

### Helpful Resources:
* JSON Schemas: https://json-schema.org/specification.html
* Confluent JSON Schema Considerations: https://docs.confluent.io/platform/current/schema-registry/fundamentals/serdes-develop/serdes-json.html
* Existing Enrollment Ingestion API Swagger: https://api-enroll-cmtx-111.cmtx-aws.com/swagger/index.html 
* Patient FHIR JSON Schema (with link to full schema): https://hl7.org/fhir/patient.schema.json.html
* Connect Enrollment Repo: https://github.com/caremetx/eenrollment-fhirparser
* Requirements Doc For Connect Intake: https://caremetx.sharepoint.com/:x:/r/sites/Product/Shared%20Documents/Enterprise%20Apps/Interoperability/Enrollment_DA_ConnectSRCreation_MappingDataSpecification.xlsx?d=wd2e55290e9414c869fd0a0d5db67adbb&csf=1&web=1&e=23CcrV

## Creating the RAPID Schema:

We took the following steps:
1. Identified the schema (through an example request body) for the FHIR Enrollment Ingestion API (Done)
2. Identified the schema (through the Swagger link above) for the Connect Enrollment API (Not specific to FHIR and Syfovre's needs) (Done)
3. Identified the required entities/fields (through the Requirements Doc for Connect Intake, link above) for Connect Enrollment (Done)
4. Articulate the result of 1 as a FHIR conforming JSON schema
    1. Started with a base JSON schema template for top level attributes, including a spot for definitions.
    2. Then copyied a JSON Schema from FHIR's official resources (starting with Bundle) into the definitions list.
    3. For a new schema, we remove the non-required attributes that are not part of the expected request body from step 1
    4. For a schema that was already present (because it was copied to support a prior schema), we ensure the current list of attributes is sufficient. If not, the missing attributes are re-added to the RAPID schema
    5. If an attribute in the request body is not present in the FHIR Schema (due to version differences), we instead included the attribute it was mapped to from v4 to v5 (Note: no attributes did not have something to map to. i.e., nothing relevant to this chema was effectively removed from the schema)
    6. If an attribute is required by the FHIR schema and not present in the request body from step 1, then it's a new field for v5 and must be included.
5. Remove elements from step 4 that are not passed to Connect
4. Pair down and extend the request from step 1 to match the information needed for step 2's schema.
5. Articulate the outcome of step 3 as a FHIR conforming JSON schema
6. Modify additional attributes as needed to setup for evolution with backwards compatibility in the confluent registry