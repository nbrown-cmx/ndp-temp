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
4. Articulate the result of 1 as a FHIR conforming JSON schema (Done)
    1. Started with a base JSON schema template for top level attributes, including a spot for definitions.
    2. Then copyied a JSON Schema from FHIR's official resources (starting with Bundle) into the definitions list.
    3. For a new schema, we remove the non-required attributes that are not part of the expected request body from step 1
    4. For a schema that was already present (because it was copied to support a prior schema), we ensure the current list of attributes is sufficient. If not, the missing attributes are re-added to the RAPID schema
    5. If an attribute in the request body is not present in the FHIR Schema (due to version differences), we instead included the attribute it was mapped to from v4 to v5 (Note: no attributes did not have something to map to. i.e., nothing relevant to this chema was effectively removed from the schema)
    6. If an attribute is required by the FHIR schema and not present in the request body from step 1, then it's a new field for v5 and must be included.
5. Remove elements from step 4 that are not passed to Connect (as identified by docs in steps 2 and 3) (Done - see note on QuestionaireResponse)
6. Identify fields in the schema that are REQUIRED for Intake into Connect. Note this in supporting documentation.
7. Modify attributes as needed to setup for evolution with backwards compatibility in the confluent registry (Done - see note on additionalFields)

### Note on Questionaire Response
The Questionaire Response fields from part 1 were specifically left out from the exercise. After discovering the content had no relation to a questionairre and instead was being used as a catch all, it was decided to leave out anything from this section that was not required. If something was discovered that was required, we would find a more appropriate place within the FHIR schema to provide it from. No fields were found to be required after comparing to the docs from step 3.

### Note on additionalFields
To ensure messages conform to the FHIR Schema, any JSON schema should set additionalFields to false for all definitions. This is because the FHIR Schema only allows for controlled extensions to schema through the use of the defined `extension` property or other related attributes. However, if we want to be able to evolve the schema for a Kafka Producer by adding new fields while allowing consumers to be at the old schema, then the 'old' schema needs to NOT enforce the absence of additional fields by setting additionalFields to true. This conflicts with the needs of FHIR.

So the choice is between:
1) Set additionalFields to false
    *Ensures messages made by our producers match the FHIR schema
    *Forces any future additional fields to be added to schemas on a new topic
2) Set additionalFields to true
    *Will ensure attributes defined in the schema match FHIR, but does not validate the lack of extraneous fields from FHIR
    *Allows future additional fields to be added to schemas for the existing topic

We chose option 2 in favor of evolution over complete FHIR validation.