# System design series - Availability - Consistency and Partition tolerance with PostgreSQL

> For now this is not working, and a gave up spending more time on it. The patroni docker image is not respecting
> environment variables and therefore the instances are not working on a cluster, rather they are working as 2
> independent instances. I will try to fix this later, but for now I will move on to the next topic.

This repo will have instructions on how to setup a PostgreSQL cluster in a CP configuration, and
how to see the effects of network partitions on the cluster.

## Dockerfile ( compose )

Sets up a etcd as the consensus provider, enabling partition tolerance and allowing nodes to coordinate
leader election and failover.

Patroni1 and Patroni2 are PostgreSQL nodes that are part of the cluster and managed by Patroni for automaic failover
and replication.

Patroni is configured to have a master and a replica, and the replica is read-only.

## Instructions

- Start the cluster

```bash
docker-compose up -d
```

After starting, you should have:

- Etcd accessible at localhost:2379.
- Patroni1 (leader) on port 5433.
- Patroni2 (replica) on port 5434.


- Insert data into the replica and see data on the master

```ssh
docker exec -it patroni2 psql -U postgres -h localhost -c "CREATE TABLE test (id SERIAL PRIMARY KEY, name VARCHAR(50));"
docker exec -it patroni2 psql -U postgres -h localhost -c "INSERT INTO test (name) VALUES ('Test Row');"
```

```ssh
docker exec -it patroni1 psql -U postgres -h localhost -c "SELECT * FROM test;"
```

