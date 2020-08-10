#DTR Execution

There are multiple ways in which the CQL execution for DTR can be performed. Each way has its own advantages and disadvantages. The basic assumption that we have in DTR is the payer defines the content that needs to be collected as a part of the DTR process. This includes the response to the Questionnaire and the other FHIR resources to be collected from the EHR / Practice management systems. The FHIR Library resource is used to define various components for implementing Documents, Templates and Rules  implementation guide.

The following are the primary components in the Library Resources

1.	Main CQL: This is the CQL in plain format and is captured in the content section of the Library. The content type is marked as “text/cql”. This is in human readable format. If the elm format is not provided, this portion is translated by the execution engine into elm and then executed.
2.	ELM/JSON: This is parsed form of CQL and the content type is marked as “application/elm+json”. 
3.	Data Requirements: This section is needed for two purposes. 
a.	When the payer needs more resources than what is being used by the CQL.
b.	When the execution engine does not have a mechanism to invoke the data retrieval from the EHR FHIR server.
4.	Related Artifacts: This section provides the list of other libraries that this library depends upon. This section is usually populated with common libraries like FHIR Helpers etc.

Implementation choices
There are several ways to implements the CQL execution but we are describing to the most practical approaches and the advantages and disadvantages of each of these approaches.
<ol>
<li>Execute with in smart on FHIR app: In this approach, the CQL execution is performed with in the javascript portion of a web based smart on fhir app. The data never leaves the EHR to an external server. Typical implementations of CQL execution doesn’t support retrieving data from the EHR FHIR server. Hence the FHIR calls described in “Data requirements” section of the library are executed first and the data is passed on to the CQL execution engine. The resulting data is also attached along with the Questionnaire response while submitting/storing the documentation. This approach optimizes on the usage of the network. However the amount of redundant data captured is high and also has the potential to put heavy load on the browser component of the EHR presenting the smart on FHIR app.
</li><li>Execute externally : There are third party systems which can execute CQL and has the functionality to retrieve the data from the EHR FHIR server given the right credentials. This may increase the network traffic but will optimize the amount of data captured and used. The CQL should not only take care of presenting the data that is used to populate the questionnaire but also take care of extracting the additional data that is needed by the payer from the EHR. This method will not work if the EHR sits behind a Firewall which is not accessible by the third party system. 
</li><li>Execute externally but supply the data extracted from “Data Requirements”: This is same as the method described above but the FHIR calls described by data requirements are executed first. The data is then passed on to the third party system and CQL is executed and results are returned back to the smart app.
</li></ol>
In all the methods, it is assumed that the Patient resource and DeviceRequest/ServiceRequest/MedicationRequest are passed on to the CQL as execution context.

To leave the choice of implementation to the implementors and not the payers, we recommend the following to be added to the implementation Guide.

1.	The CQL in ELM/JSON should always be added to the Library resource irrespective of whether the human readable form of CQL is available or not.
2.	The data requirements should always be present in the library and must be able to retrieve at least the data that is needed by the payer.
3.	The CQL definition should take in Patient resource and DeviceRequest/ServiceRequest/MedicationRequest as part of the execution context. This brings in the uniformity across all CQLs.
4.	The CQL when executed through an engine which has retrieval capabilities should be able to retrieve everything that a payer needs. 
5.	Though we recommend that both CQL and data requirements should be present in the library, they should be able to extract all the data that a payer needs independent of each other. 


