---
layout: post
---

Salesforce Apex REST

Here are some guideline that I follow when I create a Apex REST Api.


Here are some bullet points that we should cover on this REST API

1. We should be able to return a response object as JSON
2. We should be able to return an Error Object as JSON
3. We should be flexible on changing the response if that is needed.
4. We should be able to specify our own response code.
5. The response should be aligned with Salesforce.
6. 


{% highlight apex %}

@RestResource(urlMapping='/people/v1/*')
global with sharing class PeopleController {

    @HttpGet
    global static void getOnePerson() {


        RestRequest requestContext = RestContext.request;
        RestResponse responseContext = RestContext.response;
        
        // Here we make sure that our response will always be as JSON format.
        responseContext.addHeader('Content-Type', 'application/json');

        try {
            Map<String, String> reqParamsMap = requestContext.params;
            Integer totalParameterExpected = 1;
            if (reqParamsMap.size() != totalParameterExpected) {
                throw new CustomException('We didn\'t receive 1 parameters!');
            }

            if (
                !reqParamsMap.containsKey('personId')
            ) {
                throw new CustomException(
                    'We didn\'t receive all the expected parameters!' + JSON.serialize(reqParamsMap)
                );
            }

            string pPersonId = reqParamsMap.get('personId');

            PersonService personService = new PersonService();
            
            Person objPerson = personService.getOnePersonByPersonId(pPersonId);
            
            List<PeopleDTO> listOfPeopleDTO = new List<PeopleDTO>();
            listOfPeopleDTO.add(objPerson.getDTO());
            
            objResponse.statuscode = 200;
            objResponse.responseBody = Blob.valueOf(JSON.serialize(listOfPeopleDTO));
        } catch (CustomException ex) {
            new HTTPResponseCodeException(400, 'FS-FPC-FE', ex.getMessage())
                .setSFResponseError(objResponse);
        } catch (Exception ex) {
            new HTTPResponseCodeException(
                    500,
                    'FS-FPC-EX',
                    'Error unexpected! [' +
                    ex.getMessage() +
                    '] StackTrace[' +
                    ex.getStackTraceString() +
                    ']'
                )
                .setSFResponseError(objResponse);
        }
    }

}
{% endhighlight %}


Here are some comments from this class:

1. The endpoint will be: services/apexrest/person/v1
2. 



