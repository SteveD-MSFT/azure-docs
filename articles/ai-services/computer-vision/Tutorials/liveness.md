---
title: Detect liveness in faces
description: In this Tutorial, you learn how to Detect liveness in faces, using both server-side code and a client-side mobile application.
author: PatrickFarley
ms.author: pafarley
ms.service: azure-ai-vision
ms.custom:
  - ignite-2023
ms.topic: tutorial
ms.date: 11/06/2023
---

# Tutorial: Detect liveness in faces

Face Liveness detection can be used to determine if a face in an input video stream is real (live) or fake (spoof). This is a crucial building block in a biometric authentication system to prevent spoofing attacks from imposters trying to gain access to the system using a photograph, video, mask, or other means to impersonate another person.

The goal of liveness detection is to ensure that the system is interacting with a physically present live person at the time of authentication. Such systems have become increasingly important with the rise of digital finance, remote access control, and online identity verification processes.

The liveness detection solution successfully defends against a variety of spoof types ranging from paper printouts, 2d/3d masks, and spoof presentations on phones and laptops. Liveness detection is an active area of research, with continuous improvements being made to counteract increasingly sophisticated spoofing attacks over time. Continuous improvements will be rolled out to the client and the service components over time as the overall solution gets more robust to new types of attacks.

[!INCLUDE [liveness-sdk-gate](../includes/liveness-sdk-gate.md)]


## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/)
- Your Azure account must have a **Cognitive Services Contributor** role assigned in order for you to agree to the responsible AI terms and create a resource. To get this role assigned to your account, follow the steps in the [Assign roles](/azure/role-based-access-control/role-assignments-steps) documentation, or contact your administrator. 
- Once you have your Azure subscription, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Create a Face resource"  target="_blank">create a Face resource</a> in the Azure portal to get your key and endpoint. After it deploys, select **Go to resource**. 
    - You need the key and endpoint from the resource you create to connect your application to the Face service. You'll paste your key and endpoint into the code later in the quickstart.
    - You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.
- Access to the Azure AI Vision Face Client SDK for mobile (IOS and Android). To get started, you need to apply for the [Face Recognition Limited Access features](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUQjA5SkYzNDM4TkcwQzNEOE1NVEdKUUlRRCQlQCN0PWcu) to get access to the SDK. For more information, see the [Face Limited Access](/legal/cognitive-services/computer-vision/limited-access-identity?context=%2Fazure%2Fcognitive-services%2Fcomputer-vision%2Fcontext%2Fcontext) page.

## Perform liveness detection

The liveness solution integration involves two different components: a mobile application and an app server/orchestrator.

### Integrate liveness into mobile application 

Once you have access to the SDK, follow instruction in the [azure-ai-vision-sdk](https://github.com/Azure-Samples/azure-ai-vision-sdk) GitHub repository to integrate the UI and the code into your native mobile application. The liveness SDK supports both Java/Kotlin for Android and Swift for iOS mobile applications:
- For Swift iOS, follow the instructions in the [iOS sample](https://aka.ms/azure-ai-vision-face-liveness-client-sdk-ios-readme) 
- For Kotlin/Java Android, follow the instructions in the [Android sample](https://aka.ms/liveness-sample-java) 

Once you've added the code into your application, the SDK will handle starting the camera, guiding the end-user to adjust their position, composing the liveness payload, and calling the Azure AI Face cloud service to process the liveness payload.

### Orchestrate the liveness solution

The high-level steps involved in liveness orchestration are illustrated below:  

:::image type="content" source="../media/liveness/liveness-diagram.jpg" alt-text="Diagram of the liveness workflow in Azure AI Face." lightbox="../media/liveness/liveness-diagram.jpg":::

1. The mobile application starts the liveness check and notifies the app server. 

1. The app server creates a new liveness session with Azure AI Face Service. The service creates a liveness-session and responds back with a session-authorization-token.  
    
    ```json
    Request:
    curl --location '<insert-api-endpoint>/face/v1.1-preview.1/detectliveness/singlemodal/sessions' \
    --header 'Ocp-Apim-Subscription-Key:<insert-api-key>
    --header 'Content-Type: application/json' \
    --data '{
      "livenessOperationMode": "passive",
      "deviceCorrelationId": "723d6d03-ef33-40a8-9682-23a1feb7bccd"
    }'
     
    Response:
    {
        "sessionId": "a6e7193e-b638-42e9-903f-eaf60d2b40a5",
        "authToken": <session-authorization-token>
    }
    ```

1. The app server provides the session-authorization-token back to the mobile application. 

1. The mobile application provides the session-authorization-token during the Azure AI Vision SDK’s initialization. 

    ```kotlin
    mServiceOptions?.setTokenCredential(com.azure.android.core.credential.TokenCredential { _, callback ->
        callback.onSuccess(com.azure.android.core.credential.AccessToken("<INSERT_TOKEN_HERE>", org.threeten.bp.OffsetDateTime.MAX))
    })
    ```
    
    ```swift
    serviceOptions?.authorizationToken = "<INSERT_TOKEN_HERE>"
    ```

1. The SDK then starts the camera, guides the user to position correctly and then prepares the payload to call the liveness detection service endpoint. 
 
1. The SDK calls the Azure AI Vision Face service to perform the liveness detection. Once the service responds, the SDK will notify the mobile application that the liveness check has been completed. 

1. The mobile application relays the liveness check completion to the app server. 

1. The app server can now query for the liveness detection result from the Azure AI Vision Face service. 
    
    ```json
    Request:
    curl --location '<insert-api-endpoint>/face/v1.1-preview.1/detectliveness/singlemodal/sessions/a3dc62a3-49d5-45a1-886c-36e7df97499a' \
    --header 'Ocp-Apim-Subscription-Key: <insert-api-key>
    
    Response:
    {
        "status": "ResultAvailable",
        "result": {
            "id": 1,
            "sessionId": "a3dc62a3-49d5-45a1-886c-36e7df97499a",
            "requestId": "cb2b47dc-b2dd-49e8-bdf9-9b854c7ba843",
            "receivedDateTime": "2023-10-31T16:50:15.6311565+00:00",
            "request": {
                "url": "/face/v1.1-preview.1/detectliveness/singlemodal",
                "method": "POST",
                "contentLength": 352568,
                "contentType": "multipart/form-data; boundary=--------------------------482763481579020783621915",
                "userAgent": "PostmanRuntime/7.34.0"
            },
            "response": {
                "body": {
                    "livenessDecision": "realface",
                    "target": {
                        "faceRectangle": {
                            "top": 59,
                            "left": 121,
                            "width": 409,
                            "height": 395
                        },
                        "fileName": "video.webp",
                        "timeOffsetWithinFile": 0,
                        "imageType": "Color"
                    },
                    "modelVersionUsed": "2022-10-15-preview.04"
                },
                "statusCode": 200,
                "latencyInMilliseconds": 1098
            },
            "digest": "537F5CFCD8D0A7C7C909C1E0F0906BF27375C8E1B5B58A6914991C101E0B6BFC"
        },
        "id": "a3dc62a3-49d5-45a1-886c-36e7df97499a",
        "createdDateTime": "2023-10-31T16:49:33.6534925+00:00",
        "authTokenTimeToLiveInSeconds": 600,
        "deviceCorrelationId": "723d6d03-ef33-40a8-9682-23a1feb7bccd",
        "sessionExpired": false
    }
    
    ```

## Perform liveness detection with face verification

Combining face verification with liveness detection enables biometric verification of a particular person of interest with an added guarantee that the person is physically present in the system. 
There are two parts to integrating liveness with verification:
1.	Select a good reference image.
2.	Set up the orchestration of liveness with verification.

:::image type="content" source="../media/liveness/liveness-verify-diagram.jpg" alt-text="Diagram of the liveness-with-verify workflow of Azure AI Face." lightbox="../media/liveness/liveness-verify-diagram.jpg":::

### Select a good reference image

Use the following tips to ensure that your input images give the most accurate recognition results.

#### Technical requirements:
[!INCLUDE [identity-input-technical](../includes/identity-input-technical.md)]
* You can utilize the `qualityForRecognition` attribute in the [face detection](../how-to/identity-detect-faces.md) operation when using applicable detection models as a general guideline of whether the image is likely of sufficient quality to attempt face recognition on. Only `"high"` quality images are recommended for person enrollment and quality at or above `"medium"` is recommended for identification scenarios.

#### Composition requirements:
-	Photo is clear and sharp, not blurry, pixelated, distorted, or damaged. 
-	Photo is not altered to remove face blemishes or face appearance.
-	Photo must be in an RGB color supported format (JPEG, PNG, WEBP, BMP). Recommended Face size is 200 pixels x 200 pixels. Face sizes larger than 200 pixels x 200 pixels will not result in better AI quality, and no larger than 6MB in size.
-	User is not wearing glasses, masks, hats, headphones, head coverings, or face coverings. Face should be free of any obstructions.
-	Facial jewelry is allowed provided they do not hide your face. 
-	Only one face should be visible in the photo.
-	Face should be in neutral front-facing pose with both eyes open, mouth closed, with no extreme facial expressions or head tilt.
-	Face should be free of any shadows or red eyes. Please retake photo if either of these occur.
-	Background should be uniform and plain, free of any shadows. 
-	Face should be centered within the image and fill at least 50% of the image.

### Set up the orchestration of liveness with verification.

The high-level steps involved in liveness with verification orchestration are illustrated below:
1.	Provide the verification reference image by either of the following two methods:
    - The app server provides the reference image when creating the liveness session.

        ```json
        Request:
        curl --location '<insert-api-endpoint>/face/v1.1-preview.1/detectlivenesswithverify/singlemodal/sessions' \
        --header 'Ocp-Apim-Subscription-Key: <api_key>' \
        --form 'Parameters="{
          \"livenessOperationMode\": \"passive\",
          \"deviceCorrelationId\": \"723d6d03-ef33-40a8-9682-23a1feb7bccd\"
        }"' \
        --form 'VerifyImage=@"test.png"'
        
        Response:
        {
            "verifyImage": {
                "faceRectangle": {
                    "top": 506,
                    "left": 51,
                    "width": 680,
                    "height": 475
                },
                "qualityForRecognition": "high"
            },
            "sessionId": "3847ffd3-4657-4e6c-870c-8e20de52f567",
            "authToken":<session-authorization-token>
        }
        
        ```

    - The mobile application provides the reference image when initializing the SDK.

        ```kotlin
        val singleFaceImageSource = VisionSource.fromFile("/path/to/image.jpg")
        mFaceAnalysisOptions?.setRecognitionMode(RecognitionMode.valueOfVerifyingMatchToFaceInSingleFaceImage(singleFaceImageSource))
        ```

        ```swift
        if let path = Bundle.main.path(forResource: "<IMAGE_RESOURCE_NAME>", ofType: "<IMAGE_RESOURCE_TYPE>"),
           let image = UIImage(contentsOfFile: path),
           let singleFaceImageSource = try? VisionSource(uiImage: image) {
            try methodOptions.setRecognitionMode(.verifyMatchToFaceIn(singleFaceImage: singleFaceImageSource))
        }
        ```

1. The app server can now query for the verification result in addition to the liveness result.
    
    ```json
    Request:
    curl --location '<insert-api-endpoint>/face/v1.1-preview.1/detectlivenesswithverify/singlemodal/sessions/3847ffd3-4657-4e6c-870c-8e20de52f567' \
    --header 'Content-Type: multipart/form-data' \
    --header 'apim-recognition-model-preview-1904: true' \
    --header 'Authorization: Bearer.<session-authorization-token> \
    --form 'Content=@"video.webp"' \
    --form 'Metadata="<insert-metadata>"
    
    Response:
    {
        "status": "ResultAvailable",
        "result": {
            "id": 1,
            "sessionId": "3847ffd3-4657-4e6c-870c-8e20de52f567",
            "requestId": "f71b855f-5bba-48f3-a441-5dbce35df291",
            "receivedDateTime": "2023-10-31T17:03:51.5859307+00:00",
            "request": {
                "url": "/face/v1.1-preview.1/detectlivenesswithverify/singlemodal",
                "method": "POST",
                "contentLength": 352568,
                "contentType": "multipart/form-data; boundary=--------------------------590588908656854647226496",
                "userAgent": "PostmanRuntime/7.34.0"
            },
            "response": {
                "body": {
                    "livenessDecision": "realface",
                    "target": {
                        "faceRectangle": {
                            "top": 59,
                            "left": 121,
                            "width": 409,
                            "height": 395
                        },
                        "fileName": "video.webp",
                        "timeOffsetWithinFile": 0,
                        "imageType": "Color"
                    },
                    "modelVersionUsed": "2022-10-15-preview.04",
                    "verifyResult": {
                        "matchConfidence": 0.9304124,
                        "isIdentical": true
                    }
                },
                "statusCode": 200,
                "latencyInMilliseconds": 1306
            },
            "digest": "2B39F2E0EFDFDBFB9B079908498A583545EBED38D8ACA800FF0B8E770799F3BF"
        },
        "id": "3847ffd3-4657-4e6c-870c-8e20de52f567",
        "createdDateTime": "2023-10-31T16:58:19.8942961+00:00",
        "authTokenTimeToLiveInSeconds": 600,
        "deviceCorrelationId": "723d6d03-ef33-40a8-9682-23a1feb7bccd",
        "sessionExpired": true
    }
    ```

## Clean up resources

If you want to clean up and remove an Azure AI services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

* [Portal](../../multi-service-resource.md?pivots=azportal#clean-up-resources)
* [Azure CLI](../../multi-service-resource.md?pivots=azcli#clean-up-resources)

## Next steps

See the Azure AI Vision SDK reference to learn about other options in the liveness APIs.

- [Kotlin (Android)](https://aka.ms/liveness-sample-java)
- [Swift (iOS)](https://aka.ms/azure-ai-vision-face-liveness-client-sdk-ios-readme)
