# Run an LRA Sample Application

## Introduction

Run a sample application that uses the Long Running Action (LRA) transaction protocol to book a trip and understand how you can use Transaction Manager for Microservices to coordinate the transactions. Using samples is the fastest way for you to get familiar with Transaction Manager for Microservices.
The sample application code is available in the Transaction Manager for Microservices distribution. The Transaction Manager for Microservices library files are already integrated with the sample application code.

Estimated Time: 10 minutes

### About LRA Sample Application

The sample application demonstrates how you can develop microservices that participate in LRA transactions while using Transaction Manager for Microservices to coordinate the transactions. When you run the application, it makes a provisional booking by reserving a hotel room and flight ticket. Only when you provide approval to confirm the booking, the booking of the hotel room and flight ticket is confirmed. If you cancel the provisional booking, the hotel room and flight ticket that was blocked is released and the booking is canceled. The flight service in this example allows only two confirmed bookings by default. After two confirmed bookings, the flight service rejects any additional booking requests and that would lead to the trip booking failure and cancellation (compensation) of a provisionally booked hotel booking within the trip.

The following figure shows a sample LRA application, which contains several microservices, to demonstrate how you can develop microservices that participate in LRA transactions.
![Microservices in sample LRA application](./images/lra-sample-app.png)

For more details, see [About the Sample LRA Application](https://docs.oracle.com/en/database/oracle/transaction-manager-for-microservices/22.3/tmmdg/set-sample-applications.html#GUID-C5332159-BD13-4210-A02E-475107919FD9) in *Transaction Manager for Microservices Developer Guide*.

### Objectives

In this lab, you will:

* Configure Minikube
* Start a tunnel between Minikube and Transaction Manager for Microservices
* Run the LRA sample application

### Prerequisites

This lab assumes you have:

* An Oracle Cloud account
* Successfully completed all previous labs
* Logged in using remote desktop URL as oracle user. If you have connected to your instance via an SSH terminal using auto-generated SSH Keys as opc user, then change user to oracle before proceeding with the next step.

   ```text
    <copy>
    sudo su - oracle
    </copy>
    ```

## Task 1: Configure Minikube

Follow the instructions in this section to configure Minikube, and then run a sample application.

1. Open a new terminal by clicking on Activities on the top left in the remote desktop window and run the following command to start Minikube.

    ```text
    <copy>
    minikube start
    </copy>
    ```

In rare situations, if you see an error as shown below then it would indicate a failure in provisioning. In such cases, destroy the stack resources and delete the stack by performing Task 4 below. Then recreate the stack by performing the steps in Lab 2.
![minikube start error](./images/minikube-start-error.png)

2. Verify that all resources, such as pods and services, are ready before proceeding to the next task. Use the following command to retrieve the list of resources in the namespace `otmm` and their status.

    ```text
    <copy>
    kubectl get all -n otmm
    </copy>
    ```

    An output as shown in the example below is displayed.

   ![Public IP address of ingress gateway](./images/get-all-resources-ready.png)

## Task 2: Start a tunnel

Before you start a transaction, you must start a tunnel between Minikube and Transaction Manager for Microservices.

1. Run the following command in a new terminal to start a tunnel. Keep this terminal window open.

    ```text
    <copy>
    minikube tunnel
    </copy>
    ```

2. In a new terminal, run the following command to note down the external IP address of the Istio ingress gateway.

    ```text
    <copy>
    kubectl get svc istio-ingressgateway -n istio-system
    </copy>
    ```

    From the output note down the value of `EXTERNAL-IP`, which is the external IP address of the Istio ingress gateway. You will provide this value in the next step.

    **Example output**

    ![Public IP address of ingress gateway](./images/ingress-gateway-ip-address.png)

    Let's consider that the external IP in the above example is 192.0.2.117.

3. Set the URL for the Trip Manager service, which is the transaction initiator service.

    **Command syntax**

    ```text
   <copy>
    export TRIP_SERVICE_URL=http://copied-external-IP-adress/trip-service/api/trip
   </copy>
    ```

    **Example command**

    ```text
    <copy>
    export TRIP_SERVICE_URL=http://192.0.2.117/trip-service/api/trip
    </copy>
    ```

## Task 3: Run the LRA sample application

Run the sample LRA application to book a hotel room and flight ticket.

1. Run the Trip Client application.

    ```text
    <copy>
    cd /home/oracle/OTMM/otmm-22.3/samples/lra/lrademo/trip-client
    java -jar target/trip-client.jar
    </copy>
    ```

    The Trip Booking Service console is displayed.

2. Type **y** to confirm that you want to run the LRA sample application, and then press Enter.
The sample application provisionally books a hotel room and a flight ticket and displays the details of the provisional booking.

3. Type **y** to confirm the provisional booking, and then press Enter.

Your booking is confirmed and information about your confirmed booking is displayed.

![Details of the confirmed booking](./images/lra-confirmation.png)

4. Call the Trip-Service, Hotel Service and Flight Service REST APIs to view the list of the trip bookings, hotel bookings and flight bookings.

   **Example command for Trip-Service**

    ```text
    <copy>
    curl --location --request GET http://192.0.2.117/trip-service/api/trip | jq
    </copy>
    ```
   An example output for Trip-Service is shown below. The type is Trip and the status is CONFIRMED.
![Details of the confirmed booking](./images/trip-confirmation-json.png)

   **Example command for Hotel Service**

    ```text
    <copy>
    curl --location --request GET http://192.0.2.117/hotelService/api/hotel | jq
    </copy>
    ```
   **Example command for Flight Service**

    ```text
    <copy>
    curl --location --request GET http://192.0.2.117/flightService/api/flight | jq
    </copy>
    ```
You may now **proceed to the next lab**. If you do not want to proceed further and would like to finish the livelabs and clean up the resources, then perform task 4 below.

<!-- ## Task 4: Clean up the livelabs stack

Perform this task only if you want to clean up the livelabs stack provisioned using Resource Manager. Performing this task will delete all the stack resources including the remote desktop instance.


1. Open the navigation menu and click Developer Services. Under Resource Manager, click Stacks.
2. Choose a compartment that you have permission to work in (on the left side of the page).
3. Click the name of the stack that you want.
4. The Stack details page opens.
5. Click on Destroy to delete the stack resources.
6. Confirm the operation when prompted.
7. After the Destroy job is completed, go to More actions on the Stack details page and then select Delete stack.
8. Confirm the operation when prompted.

![Destroy the stack resources](./images/destroy-stack.png)
![Delete the stack](./images/delete-stack.png) -->

## Learn More

* [Develop Applications with LRA](https://doc.oracle.com/en/database/oracle/transaction-manager-for-microservices/22.3/tmmdg/develop-lra-applications.html#GUID-63827BB6-7993-40B5-A753-AC42DE97F6F4)

## Acknowledgements

* **Author** - Sylaja Kannan, Principal User Assistance Developer
* **Contributors** - Brijesh Kumar Deo
* **Last Updated By/Date** - Sylaja, October 2022
