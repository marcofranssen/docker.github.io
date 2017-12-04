---
title: Deploy a simple stateless app with RBAC
description: Learn how to deploy a simple application and customize access to resources.
keywords: rbac, authorize, authentication, users, teams, UCP, Docker
redirect_from:
- /ucp/
ui_tabs:
- version: ucp-3.0
  orhigher: true
- version: ucp-2.2
  orlower: true
---

{% if include.ui %}
{% if include.version=="ucp-3.0" %}

This tutorial explains how to deploy a nginx web server and limit access to one
team with role-based access control (RBAC).

## Scenario

You are the Docker EE system administrator at Acme Company and need to configure
permissions to company resources. The best way to do this is to:

- Build the organization with teams and users.
- Define roles with allowable operations per resource types (can run containers, etc.).
- Create collections or namespaces for storing actual resources.
- Create grants that join team + role + resources.

## Build the organization

Add the organization, `acme-datacenter`, and create three teams according to the
following structure:

```
acme-datacenter
├── dba
│   └── Alex Alutin
├── dev
│   └── Bett Bhatia
└── ops
    └── Chad Chavez
```

See: [Create and configure users and teams](./rbac-basics-create-subjects.md).

## Kubernetes deployment

In this section, we deploy `nginx` with Kubernetes. See [Swarm stack](#swarm-stack)
for the same exercise with Swarm.

### Create namespace

Create a namespace to logically store the nginx application:

1. Click **Kubernetes** > **Namespaces**.
2. Paste the following manifest in the terminal window and click **Create**.

```
apiVersion: v1
kind: Namespace
metadata:
  name: nginx-namespace
```

### Define roles

You can use the built-in roles or define your own. For this exercise, create a
simple role for the ops team:

1. Click **Roles** under **User Management**.
2. Click **Create Role**.
3. On the **Details** tab, name the role `Kube Deploy`.
4. On the **Operations** tab, check all **Kubernetes Deployment Operations**.
5. Click **Create**.

See: [Create and configure users and teams](./rbac-basics-define-roles.md).

### Grant access

Grant the ops team (and only the ops team) access to nginx-namespace with the
custom role, **Kube Deploy**.

```
acme-datacenter/ops + Kube Deploy + nginx-namespace
```

### Deploy Nginx

You've configured Docker EE. The `ops` team can now deploy `nginx`.

1. Log on to UCP as chad (on the `ops`team).
2. Click **Kubernetes** > **Namespaces**.
3. Paste the following manifest in the terminal window and click **Create**.

```
apiVersion: apps/v1beta2  # Use apps/v1beta1 for versions < 1.8.0
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

4. Log on to UCP as each user and ensure that:
- `dba` (alex) cannot see `nginx-namespace`.
- `dev` (bett) cannot see `nginx-namespace`.

## Swarm Stack

In this section, we deploy `nginx` as a Swarm service. See [Kubernetes Deployment](#kubernetes-deployment)
for the same exercise with Swarm.

### Create collection paths

Create a collection for nginx resources, nested under the `/Shared` collection:

```
/
├── System
└── Shared
    └── nginx-collection
```

> **Tip**: To drill into a collection, click **View Children**.

See: [Group and isolate cluster resources](./rbac-basics-group-resources.md).

### Define roles

You can use the built-in roles or define your own. For this exercise, create a
simple role for the ops team:

1. Click **Roles** under **User Management**.
2. Click **Create Role**.
3. On the **Details** tab, name the role `Swarm Deploy`.
4. On the **Operations** tab, check all **Service Operations**.
5. Click **Create**.

See: [Create and configure users and teams](./rbac-basics-define-roles.md).

### Grant access

Grant the ops team (and only the ops team) access to nginx-collection with the
built-in role, **Swarm Deploy**.

```
acme-datacenter/ops + Swarm Deploy + /Shared/nginx-collection
```

See: [Grant role-access to cluster resources](./rbac-basics-grant-permissions.md).

### Deploy Nginx

You've configured Docker EE. The `ops` team can now deploy an `nginx` Swarm
service.

1. Log on to UCP as chad (on the `ops`team).
2. Click **Swarm** > **Services**.
3. Click **Create Stack**.
4. On the Details tab, enter:
   - Name: `nginx-service`
   - Image: nginx:latest
5. On the Collections tab:
   - Click `/Shared` in the breadcrumbs.
   - Select `nginx-collection`.
6. Click **Create**.
7. Log on to UCP as each user and ensure that:
   - `dba` (alex) cannot see `nginx-collection`.
   - `dev` (bett) cannot see `nginx-collection`.


{% elsif include.version=="ucp-2.2" %}

This tutorial explains how to deploy a nginx web server and limit access to one
team with role-based access control (RBAC).

## Scenario

You are the Docker EE system administrator at Acme Company and need to configure
permissions to company resources. The best way to do this is to:

- Build the organization with teams and users.
- Define roles with allowable operations per resource types (can run containers, etc.).
- Create collections or namespaces for storing actual resources.
- Create grants that join team + role + resources.

## Build the organization

Add the organization, `acme-datacenter`, and create three teams according to the
following structure:

```
acme-datacenter
├── dba
│   └── Alex Alutin
├── dev
│   └── Bett Bhatia
└── ops
    └── Chad Chavez
```

See: [Create and configure users and teams](./rbac-basics-create-subjects.md).

## Swarm Stack

In this section, we deploy `nginx` as a Swarm service. 

### Create collection paths

Create a collection for nginx resources, nested under the `/Shared` collection:

```
/
├── System
└── Shared
    └── nginx-collection
```

> **Tip**: To drill into a collection, click **View Children**.

See: [Group and isolate cluster resources](./rbac-basics-group-resources.md).

### Define roles

You can use the built-in roles or define your own. For this exercise, create a
simple role for the ops team:

1. Click **Roles** under **User Management**.
2. Click **Create Role**.
3. On the **Details** tab, name the role `Swarm Deploy`.
4. On the **Operations** tab, check all **Service Operations**.
5. Click **Create**.

See: [Create and configure users and teams](./rbac-basics-define-roles.md).

### Grant access

Grant the ops team (and only the ops team) access to nginx-collection with the
built-in role, **Swarm Deploy**.

```
acme-datacenter/ops + Swarm Deploy + /Shared/nginx-collection
```

See: [Grant role-access to cluster resources](./rbac-basics-grant-permissions.md).

### Deploy Nginx

You've configured Docker EE. The `ops` team can now deploy an `nginx` Swarm
service.

1. Log on to UCP as chad (on the `ops`team).
2. Click **Swarm** > **Services**.
3. Click **Create Stack**.
4. On the Details tab, enter:
   - Name: `nginx-service`
   - Image: nginx:latest
5. On the Collections tab:
   - Click `/Shared` in the breadcrumbs.
   - Select `nginx-collection`.
6. Click **Create**.
7. Log on to UCP as each user and ensure that:
   - `dba` (alex) cannot see `nginx-collection`.
   - `dev` (bett) cannot see `nginx-collection`.
   -
{% endif %}
{% endif %}