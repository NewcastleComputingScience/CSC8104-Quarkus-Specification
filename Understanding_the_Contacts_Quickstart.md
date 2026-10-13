**Tutorial - Understanding the Contacts Quickstart with GitHub Copilot**

# When to complete this tutorial \| Approximately 60–90 minutes

Complete this page after finishing the existing Contacts Quickstart and before starting the coursework. Allow approximately 60–90 minutes for the six exercises. Before starting, you should have:

- run the Contacts application locally;

- deployed the application to OpenShift;

- tested the main endpoints using Swagger, cURL or the method shown in the quickstart;

- confirmed that you can start, stop and inspect the running application.

This tutorial helps you understand the code you have just used before you begin adapting it for the coursework. It is not a replacement for the original quickstart and it is not a shortcut for completing coursework.

# Technical baseline

Use the Java and Quarkus versions specified in the existing Contacts Quickstart and its project configuration. The exact versions have not been verified from the materials supplied here; teaching staff should confirm them against the repository build file and quickstart before publishing this page. Do not change the project configuration unless teaching staff ask you to do so.

# How to access GitHub Copilot in this activity

Use GitHub Copilot through GitHub in the browser. You do not need an IDE extension, AI API key, or additional integration for these exercises.

1.  Open the tutorial repository or relevant code file in GitHub in your browser.

2.  Sign in using the account that has access to GitHub Copilot.

3.  Use the Copilot chat or Ask Copilot option available in the GitHub interface.

4.  Give Copilot only the smallest relevant code excerpt needed for the question.

5.  Do not paste credentials, access tokens, private information, deployment secrets, or another student’s work.

# How to use Copilot in this tutorial

Copilot can help you understand unfamiliar code, annotations, errors and framework behaviour. However, it can also be incomplete, incorrect or based on assumptions that do not match this project. In this tutorial, use Copilot as a learning assistant, not as a way of generating coursework. You should be able to explain the code and behaviour yourself.

**Pattern to follow in every exercise:**

![Five-step Copilot learning process](images/Excercise%20pattern.png)

# Exercise 1 - Understand the structure of the application

## What you will learn

- Understand how a GET /contacts request moves through the REST, service and repository layers.

- Explain the role of each layer in your own words.

## Inspect and predict before using Copilot

Open these files before using Copilot:

- ContactRestService.java

- ContactService.java

- ContactRepository.java

Before asking Copilot, predict the path of this request:

GET /contacts

Write down which class receives the HTTP request, which class contains the application logic, and which class communicates with the database.

## Ask Copilot

**Prompt to use:** *I am learning Quarkus and trying to understand the structure of this Contacts application. It has ContactRestService, ContactService and ContactRepository classes. Can you explain the responsibility of each layer and describe how a GET request for /contacts moves through them? Please explain the existing design rather than suggesting a new implementation.*

## Question the response

Ask yourself whether Copilot stayed focused on the request flow, or whether it introduced extra details that are not needed yet.

**Follow-up prompt:** *Can you walk me through the retrieveAllContacts() method line by line and explain how it fits into the request flow? Please only use the supplied code and tell me which claims I should verify.*

## Check against the code, documentation or application behaviour

Return to the code and trace the request yourself.

- Find the method that handles GET /contacts.

- Identify which service methods it calls.

- Identify which repository methods run the database queries.

- Check whether Copilot mentioned details that are true but distracting, such as caching, retainAll(), transactions or exception mapping.

## Record your conclusion briefly in your own words

Explain the path from HTTP request -\> REST layer -\> service layer -\> repository/database -\> HTTP response without using Copilot. Write 3-5 sentences. Do not copy Copilot. Explain what you now understand, what evidence supports it, and one claim you checked or questioned.

# Exercise 2 - Understand JPA annotations

## What you will learn

- Understand the purpose of the main JPA annotations in Contact.java.

- Separate general JPA behaviour from behaviour that depends on this application’s configuration.

## Inspect and predict before using Copilot

Open Contact.java. Identify annotations that you do not fully understand.

Choose a small number of important annotations first, such as @Entity, @Id, @GeneratedValue and @Column.

## Ask Copilot

**Prompt to use:** *I am learning JPA through a Quarkus application. In this class I can see annotations such as @Entity, @Id, @GeneratedValue and @Column. Can you explain what each annotation tells JPA to do and what role each one plays when a Contact object is stored in the database? Please focus on explaining the concepts rather than rewriting the class.*

## Question the response

Copilot may include useful but advanced details. Do not assume that every statement applies automatically to this project.

**Follow-up prompt:** *What would change in the behaviour of this application if @Entity were removed from the Contact class? Please distinguish between what you know from JPA and anything that depends on the specific application configuration.*

## Check against the code, documentation or application behaviour

- Check the actual Contact.java file.

- Check whether the response explains the annotations used in this project rather than unrelated JPA features.

- Mark any statements that would depend on configuration, such as schema generation, validation behaviour or when generated identifiers become available.

## Record your conclusion briefly in your own words

Explain why the annotations exist and identify one Copilot claim that would need checking against the project configuration.

**Record:** Write 3-5 sentences. Do not copy Copilot. Explain what you now understand, what evidence supports it, and one claim you checked or questioned.

# Exercise 3 - Follow a POST request

## What you will learn

- Understand the POST /contacts flow.

- Identify the external Area-service dependency that occurs before the Contact is stored.

## Inspect and predict before using Copilot

Using Swagger or cURL, look at what happens when a contact is created.

Before asking Copilot, inspect the relevant REST, service and repository code. Predict what happens between:

POST /contacts -\> contact stored in database

Write down whether you think the request goes directly to the database or whether another service is involved.

## Ask Copilot

**Prompt to use:** *I am learning how this Quarkus application works. I want to understand what happens when a POST request is made to /contacts. Using the code below, walk me through the sequence of operations from receiving the HTTP request to attempting to store the Contact. Please explain the existing control flow and point out any other service the application depends on. Do not rewrite the implementation.*

## Question the response

Check whether Copilot describes the Area service carefully. It is safer to call it an external REST-service dependency unless the supplied code proves more than that.

**Follow-up prompt:** *Which parts of this process happen inside this application, and which parts depend on something external to it? Please base your answer only on the supplied code and clearly identify anything you cannot determine from the code alone.*

## Check against the code, documentation or application behaviour

- Locate where the POST endpoint is handled.

- Locate ContactService.create().

- Find where the Area service is called.

- Find where the Contact is persisted.

- Check whether Copilot makes unsupported assumptions about the exact deployment or availability of the external service.

## Record your conclusion briefly in your own words

Summarise the POST flow and explain why creating a Contact is not simply HTTP request -\> database.

**Record:** Write 3-5 sentences. Do not copy Copilot. Explain what you now understand, what evidence supports it, and one claim you checked or questioned.

# Exercise 4 - Use Copilot to investigate a supplied POST 500 troubleshooting case

## What you will learn

- Use Copilot to support diagnosis rather than asking it to fix the code.

- Distinguish evidence from assumptions when investigating an HTTP 500 error.

## Inspect and predict before using Copilot

This is a supplied troubleshooting case. It does not rely on the Area service being unavailable during the teaching session.

Read Extracts 1–3 in the separate Supplied Log and Code Extracts document before asking Copilot. [Student Evidence Pack](Student_evidence_pack.md)

The case states that GET /contacts works, but POST /contacts returns HTTP 500. A direct test of the Area-service endpoint also fails.

Before asking Copilot, write down at least two possible explanations and one thing that cannot yet be concluded.

## Ask Copilot

**Prompt to use:** *I am learning to troubleshoot a Quarkus REST application. In this supplied case, GET /contacts works, but a valid POST to /contacts returns HTTP 500. The POST operation calls an external Area REST service before the Contact is stored. Based on the supplied HTTP result, sanitised log excerpt and direct Area-service test, what should I investigate to narrow down where the failure is occurring? Please guide me through the diagnosis rather than writing code to fix it.*

## Question the response

Copilot may produce a long list of possible causes. Your task is to decide which suggestions are supported by the supplied evidence.

**Follow-up prompt:** *The direct Area-service test also failed. How should this change my diagnosis? Does this prove that there is nothing wrong with my own POST implementation? Please separate what the evidence supports from what remains uncertain.*

## Check against the code, documentation or application behaviour

- Compare Copilot’s answer with the supplied evidence pack.

- Check ContactService.create() to confirm where the Area service is called.

- Decide whether the failed direct Area-service test proves the POST implementation is correct.

- Identify any unsupported suggestions, such as credentials, firewalls, retries or restarting services, unless the supplied evidence supports them.

## Record your conclusion briefly in your own words

State the most reasonable diagnosis and at least two things that cannot be concluded from the evidence alone.

**Record:** Write 3-5 sentences. Do not copy Copilot. Explain what you now understand, what evidence supports it, and one claim you checked or questioned.

# Exercise 5 - Understand transactions rather than memorising @Transactional

## What you will learn

Evidence reference: Extract 4 in Supplied Log and Code Extracts. [Student Evidence Pack](Student_evidence_pack.md)

- Understand what @Transactional provides for local database changes.

- Distinguish database transaction rollback from external REST calls or other side effects.

## Inspect and predict before using Copilot

Find where @Transactional is used in the Contacts application.

Before asking Copilot, write down what you think @Transactional does here.

Also write down whether you think a database transaction can undo an external REST call.

## Ask Copilot

**Prompt to use:** *I am learning Quarkus transactions using this tutorial application. The code uses @Transactional around operations that change Contact data. Can you explain what transaction boundary this creates and why a database-changing operation might need it? Explain what happens on successful completion and what may happen if the operation fails. Please explain the concept using this code rather than generating a replacement implementation.*

## Question the response

Do not accept broad claims such as “every exception automatically causes rollback” or “everything is undone if anything fails.” Ask Copilot to be precise.

**Follow-up prompt:** *Does that statement apply to every possible side effect performed by the method, including calls to external REST services? Please distinguish database transaction behaviour from external HTTP operations, and check this against the supplied ContactService.create() flow.*

## Check against the code, documentation or application behaviour

- Identify the local database operation.

- Identify the external Area REST-service call.

- Explain which part is controlled by the local transaction and which part is not automatically reversed by it.

- Check whether Copilot incorrectly treats ContactService.create() as only database work.

## Record your conclusion briefly in your own words

Explain the difference between a local database transaction and an external REST call in this tutorial.

**Record:** Write 3-5 sentences. Do not copy Copilot. Explain what you now understand, what evidence supports it, and one claim you checked or questioned.

# Exercise 6 - Understand exceptions and HTTP responses

## What you will learn

- Trace how an application error becomes an HTTP response.

- Use code evidence to predict an HTTP status and response body.

- Check what can and cannot be inferred before seeing ErrorMessage.java and the actual HTTP response.

## Inspect and predict before using Copilot

Choose one failure path. This tutorial uses InvalidAreaCodeException as the example.

Before using Copilot, try to trace:

something goes wrong -\> exception -\> HTTP response

Identify where the exception originates, where it is caught or wrapped, and where it is translated into a response.

## Ask Copilot

**Prompt to use:** *I am learning exception handling in a Quarkus REST application. This project contains application-specific exceptions and a RestServiceExceptionMapper. Using the supplied code, can you trace what happens when an InvalidAreaCodeException occurs during the creation of a Contact, from where the exception originates through to the HTTP response received by the client? Please explain the existing behaviour rather than proposing a different architecture.*

## Question the response

Copilot may sound convincing while placing responsibility in the wrong class. In a staff test, Copilot incorrectly stated that ContactValidator calls AreaService. Use the code to verify or challenge that claim.

**Follow-up prompt:** *Based only on the supplied code, which HTTP status and response body would I expect for this InvalidAreaCodeException failure? Show me exactly which parts of the code support your answer, and identify anything you cannot determine from the supplied code alone.*

## Check against the code, documentation or application behaviour

Evidence reference: Extracts 4–5 in Supplied Log and Code Extracts. [Student Evidence Pack](Student_evidence_pack.md)

- Inspect ContactService.create() and ContactValidator to check whether ContactValidator calls AreaService.

- Inspect ContactRestService.createContact() to see how InvalidAreaCodeException is handled.

- Inspect RestServiceExceptionMapper to see what it can tell you about the status and response entity.

- Then inspect ErrorMessage.java to confirm the response body structure.

- Where possible, trigger the failure through Swagger or cURL and compare your prediction with the actual HTTP response.

## Record your conclusion briefly in your own words

Explain the error path and identify one claim Copilot made that the code supports or contradicts.

**Record:** Write 3-5 sentences. Do not copy Copilot. Explain what you now understand, what evidence supports it, and one claim you checked or questioned.

# Final reminder

**Remember:** Copilot is there to support your understanding, not to replace your judgement. Always check its explanation against the actual code, the documentation, and what the application does when you test it.
