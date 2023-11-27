# Add Repository Secret

Let's add the External Secret CR for the secret that we just stored in Vault. This will allow us to fetch the secret from Vault and distribute it to the build namespace of our tenant.

## Objectives

* Deploy the `github-webhook-config` secret to build namespace.

## Key Results

* External Secret CR created in the build namespace
* Secret fetch from Vault and deployed in build namespace.

## Tutorial

1. Head over to the `apps-gitops-config` repository that we have already created.

1. If you have followed the tutorial on configuring `apps-gitops-config` repository correctly, you should already have this your tenant folder at the root of this repository.

1. Navigate to the application folder. In our case it will be `stakater-nordmart-review-api`

1. Now open up the `build` folder.

1. Create a file named `github-webhook-config.yaml` and add in the below content. Replace the Url with your application repository's Url.

    ```yaml
       apiVersion: external-secrets.io/v1beta1
       kind: ExternalSecret
       metadata:
         name: github-webhook-config
       spec:
         secretStoreRef:
           name: tenant-vault-secret-store
           kind: SecretStore
         refreshInterval: "1m0s"
         target:
           name: github-webhook-config
           creationPolicy: 'Owner'
           template:
             data:
               provider.token: "{{ .password | toString }}"
               webhook.secret: "{{ .secret | toString }}"
         data:
           - secretKey: password
             remoteRef:
               key: github-webhook-config
               property: provider.token
           - secretKey: secret
             remoteRef:
               key: github-webhook-config
               property: webhook.secret
    ```

     <div style="text-align:center"><img src="images/github-webhook-config-es.png" /></div>

1. Now open up ArgoCD and look for this External Secret. If everything was added correctly, you will see a secret created from this External Secret.

     <div style="text-align:center"><img src="images/github-webhook-config-argo.png" /></div>

1. You can also check this secret by navigation to `<tenant>-build` namespace and searching for the secret.

     <div style="text-align:center"><img src="images/github-webhook-config-secret.png" /></div>

Great! We have everything set up for creating the Repository CR.
