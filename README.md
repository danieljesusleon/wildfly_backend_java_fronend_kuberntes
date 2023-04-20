# wildfly_backend_java_fronend_kuberntes
configure an app using WildFly for the backend and Java for the frontend using a Kubernetes manifest on Digital Ocean Cloud
follow these steps:

-   Create a Digital Ocean account and login to the control panel.

-   Create a new Kubernetes cluster in Digital Ocean. Make sure to select the appropriate region, node size, and number of nodes based on your requirements.

-   Once the cluster is created, connect to the cluster using kubectl.

-   Create a new namespace for your application using the following command:

-   kubectl create namespace <namespace-name>

-   Create a secret in Kubernetes to store the database credentials. You can create the secret using the following command:

-   kubectl create secret generic <secret-name> --from-literal=username=<db-username> --from-literal=password=<db-password>

-   Replace <secret-name>, <db-username>, and <db-password> with appropriate values.

-   Create a deployment for WildFly using the following YAML manifest:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: wildfly
  namespace: <namespace-name>
spec:
  selector:
    matchLabels:
      app: wildfly
  replicas: 1
  template:
    metadata:
      labels:
        app: wildfly
    spec:
      containers:
        - name: wildfly
          image: jboss/wildfly
          ports:
            - containerPort: 8080
              protocol: TCP
          env:
            - name: DB_USERNAME
              valueFrom:
                secretKeyRef:
                  name: <secret-name>
                  key: username
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: <secret-name>
                  key: password

Replace <namespace-name> and <secret-name> with the appropriate values. This manifest deploys a single replica of WildFly and sets the database credentials as environment variables.

Create a service for WildFly using the following YAML manifest:
apiVersion: v1
kind: Service
metadata:
  name: wildfly
  namespace: <namespace-name>
spec:
  selector:
    app: wildfly
  ports:
    - name: http
      port: 80
      targetPort: 8080
      protocol: TCP
  type: LoadBalancer

This manifest creates a service for WildFly and exposes it as a load balancer on port 80.
Create a deployment for the Java frontend using the following YAML manifest:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-frontend
  namespace: <namespace-name>
spec:
  selector:
    matchLabels:
      app: java-frontend
  replicas: 1
  template:
    metadata:
      labels:
        app: java-frontend
    spec:
      containers:
        - name: java-frontend
          image: <image-name>
          ports:
            - containerPort: 80
              protocol: TCP
          env:
            - name: BACKEND_URL
              value: "http://wildfly:80"
Replace <namespace-name> and <image-name> with the appropriate values. This manifest deploys a single replica of the Java frontend and sets the URL of the backend as an environment variable.

    Create a service for the Java frontend using the following YAML manifest:
apiVersion: v1
kind: Service
metadata:
  name: java-frontend
  namespace: <namespace-name>
spec:
  selector:
    app: java-frontend
  ports:
    - name: http
      port: 80

