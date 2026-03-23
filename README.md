GENERAL INSTRUCTIONS !!! PLEASE NOTE

*********************************************************************************************************************************************************************************************************

Don't forget to edit the Frontend app's config.json with the backend url and then rebuild the image


ALWAYS FIRST BACKEND APPLICATION SHOULD BE RUN FIRST AND THEN FRONTEND


*********************************************************************************************************************************************************************************************************
once edited update frontend yaml files:
********************************************************************************************************************************************************************************************************
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
  namespace: devops-app
  labels:
    app: frontend
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: abhilash2/frontend-app:latest4
        imagePullPolicy: Always
        ports:
        - containerPort: 3000
          name: http
        livenessProbe:
          httpGet:
            path: /
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 3000
          initialDelaySeconds: 15
          periodSeconds: 5
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
  namespace: devops-app
  labels:
    app: frontend
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 3000
    protocol: TCP
    name: http
  selector:
    app: frontend
__________________________________________________________________________________________________________________________________________________________________________________________________________________________________________
edit the backend.yaml
__________________________________________________________________________________________________________________________________________________________________________________________________________________________________________
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-deployment
  namespace: devops-app
  labels:
    app: backend
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: abhilash2/backend-app:latest
        imagePullPolicy: Always
        ports:
        - containerPort: 5000
          name: http
        livenessProbe:
          httpGet:
            path: /
            port: 5000
          initialDelaySeconds: 10
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 5000
          initialDelaySeconds: 5
          periodSeconds: 5
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: backend-service
  namespace: devops-app
  labels:
    app: backend
spec:
  type: NodePort
  ports:
  - port: 5000
    targetPort: 5000
    protocol: TCP
    name: http
  selector:
    app: backend
