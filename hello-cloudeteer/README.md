# Helm Chart for Google Hello App

This Helm chart deploys the Google Hello App with MySQL as backing database.

The source code of the app can be found at: https://github.com/GoogleCloudPlatform/kubernetes-engine-samples/tree/master/hello-app

## Notes

```bash
# first create initial chart
`helm create hello-cloudeteer`

# after adding mysql dependency to Chart.yaml 
`helm dependency build`
```

I removed the HPA and Ingress from the Chart, as those are not needed.
