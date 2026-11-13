# Deploying a New Version to OpenShift Serverless

**CSC8104 – Enterprise Middleware**

## Overview

When you make changes to your application and push them to GitHub, those changes are not necessarily deployed automatically to OpenShift Serverless.

There are two separate stages:

1. **Build:** OpenShift retrieves your updated code and creates a new container image.
2. **Deploy:** OpenShift Serverless creates a new revision that runs the updated image.

**Important:** A successful build does not always mean that your application is running the new version.

This guide explains how to build and deploy a new version of an existing CSC8104 application using OpenShift **BuildConfig** and **Serverless Deployment (Knative Service)**.

You should already have deployed your application successfully using the main CSC8104 tutorial.

---

## Step 1 – Push Your Updated Code to GitHub

Make the required changes to your Quarkus application.

Commit and push these changes to the GitHub repository connected to your OpenShift application.

Check that the latest changes are visible on GitHub before continuing.

## Step 2 – Build the Updated Application

1. Open your OpenShift Developer Sandbox.
2. Select the project containing your application.
3. Navigate to **Builds → BuildConfigs**.
4. Open the BuildConfig associated with your application.
5. Click **Actions → Start Build** if a new build has not already started automatically.
6. Open the new build to monitor its progress.

OpenShift will retrieve your latest GitHub code and build a new container image.

**Wait until the build status is Complete before proceeding.**

If the build fails, check its Logs tab and resolve the errors before attempting to deploy the new version.

**Note:** GitHub commits only trigger OpenShift builds automatically when the required webhook integration is configured. Otherwise, you must start the build manually.

## Step 3 – Confirm the New Image Was Created

1. Navigate to **Builds → ImageStreams**.
2. Open the ImageStream associated with your application.
3. Find the image tag named `latest`.
4. Confirm that it was updated following your completed build.

The image's **Identifier**, beginning with `sha256:`, identifies the built container image.

The `latest` tag should now refer to the image produced by your newest build.

However, **the Serverless application may still be using an older revision**, even though the new image exists.

## Step 4 – Create a New Serverless Revision

To make OpenShift Serverless deploy the updated image, we need to update the Knative Service's revision template.

1. Navigate to **Serverless → Serving → Services**.
2. Click your application's service name.
3. Select the **YAML** tab.
4. Locate the following section:

   `spec → template → metadata → annotations`

5. Add a `buildVersion` annotation beneath any existing annotations.

For example, when deploying Version 2:

```yaml
spec:
  template:
    metadata:
      annotations:
        buildVersion: '2'
```

**Do not replace the existing YAML with this example.** Add only the `buildVersion` line to your service's existing annotations, keeping the correct indentation.

If the service already contains:

```yaml
annotations:
  autoscaling.knative.dev/target: '100'
  autoscaling.knative.dev/window: 60s
```

Update it to:

```yaml
annotations:
  autoscaling.knative.dev/target: '100'
  autoscaling.knative.dev/window: 60s
  buildVersion: '2'
```

6. Click **Save**.

Changing an annotation within `spec.template.metadata.annotations` updates the revision template and causes Knative to create a new revision.

For subsequent deployments, change the annotation to a new value:

- Version 2: `buildVersion: '2'`
- Version 3: `buildVersion: '3'`
- Version 4: `buildVersion: '4'`

The annotation value is a simple way to trigger a new revision. It is not automatically linked to your application's GitHub version number.

**Important:** Always wait for the new build to complete before updating `buildVersion`. Otherwise, the new revision may use the previous container image.

## Step 5 – Verify the New Revision

1. Navigate to **Serverless → Serving → Revisions**, or select the **Revisions** tab for your service.
2. Check that a new revision has appeared.

For example:

| Previous revision | New revision |
|---|---|
| `my-app-00001` | `my-app-00002` |

3. Wait until the new revision shows **Ready: True**.
4. Open the new revision and select the **YAML** tab.
5. Search for `imageDigest`.

Under `status → containerStatuses`, locate the `imageDigest` value.

Compare this digest with the **Identifier** for `latest` in your ImageStream from Step 3.

**If the digests match, the new revision is using the newly built container image.**

Also check that your Serverless service is Ready and directing traffic to the new revision.

## Step 6 – Test Your Updated Application

Open the public URL associated with your Serverless service and test the endpoints affected by your changes.

For example, if your application includes the supplied Contacts API, you can test:

`https://<your-application-url>/contacts`

Check that the application responds correctly and that your updated functionality behaves as expected.

If the new revision is not Ready, inspect its status, events and application logs before making further changes.

---

## Troubleshooting

| Problem | What to check |
|---|---|
| GitHub changes are not reflected in OpenShift | Confirm your changes were pushed, then start a new build |
| Build has failed | Open the build's Logs tab to identify the error |
| Build completed, but the application still uses an older revision | Update `buildVersion` in the Knative Service's revision template |
| No new revision appears | Check that you edited the **Service YAML**, under `spec.template.metadata.annotations`, and saved the change |
| New revision is not Ready | Check the revision's conditions, events and application logs |
| New revision uses the previous image | Confirm the new build completed before updating `buildVersion`, then create a further revision |
| Application is running but behavior has not changed | Verify that the expected code was committed, built and deployed; check the image digests |

---

## Key Takeaway

**Updating your application involves both building and deploying.**

The recommended process for this CSC8104 Serverless setup is:

**Push updated code → Complete new build → Confirm new image → Update `buildVersion` → Verify new revision → Test application**

Do not assume your application has been redeployed simply because OpenShift reports **Build Complete**.

This procedure was tested using a CSC8104 Quarkus quickstart application deployed with OpenShift BuildConfig and Knative Serverless. A second build produced a different container image, but no new revision appeared until the service's `buildVersion` annotation was updated. The resulting revision was Ready and its image digest matched the newer build.
