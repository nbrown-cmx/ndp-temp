# Enrollment Schemas

### Helpful Resources:
* JSON Schemas: https://json-schema.org/specification.html
* Confluent JSON Schema Considerations: https://docs.confluent.io/platform/current/schema-registry/fundamentals/serdes-develop/serdes-json.html
* Existing Enrollment Ingestion API Swagger: https://api-enroll-cmtx-111.cmtx-aws.com/swagger/index.html 
* Patient FHIR JSON Schema (with link to full schema): https://hl7.org/fhir/patient.schema.json.html


## Creating the RAPID Schema:

We took the following steps:
1. Identified the schema (through an example request body) for the FHIR Enrollment Ingestion API (Done)
2. Identified the schema (through the Swagger link above) for the Connect Enrollment API (Not specific to FHIR and Syfovre's needs) (Done)
3. Pair down and extend the request from step 1 to match the information needed for step 2's schema.
4. Articulate the outcome of step 3 as a FHIR conforming JSON schema
5. Modify additional attributes as needed to setup for evolution with backwards compatibility in the confluent registry