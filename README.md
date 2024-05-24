# Integrating Postgres with Debezium for CDC

Learn how to integreate Postgres with Redpanda via Debezium for change data capture (CDC).

This is an abbreviated 2024 version of the [Redpanda blog 2022 tutorial](https://redpanda.com/blog/change-data-capture-postgres-debezium-kafka-connect) using Docker Compose `v2.27.0`:

1. open a shell
2. `docker compose up` to run:
    1. a Redpanda broker
    2. the Redpanda console on http://localhost:8080/
    3. a postgresql instance configured with `extended.conf` and init'd with `init.sql` data
    4. Debezium connector
3. wait for init logging to finish
4. open another shell
5. `docker ps` to view running compose services
6. open http://localhost:8080/topics in a browser to view metadata topics added by Kafka Connect
7. `docker exec redpanda-debezium-1 curl -is -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @/tmp/connector.json` in the second shell to configure Debezium with a Postgresql connection
8. `docker exec redpanda-debezium-1 curl -s -H "Accept:application/json" localhost:8083/connectors/` to view the new configuration's availability
9. refresh the http://localhost:8080/topics screen to view the `shop.public.customer_addresses` topic added by the new configuration
10. select the http://localhost:8080/topics/shop.public.customer_addresses link to view the **51** messages in the `shop.public.customer_addresses` topic
11. return to the second shell and `docker exec -e PGPASSWORD=password redpanda-postgresql-1 psql -U postgres -d shop -c "update public.customer_addresses set state = 'Johannesburg' where id = 10;"` to add a row change in Postgresql
12. refresh the http://localhost:8080/topics/shop.public.customer_addresses tab to view the **52** messages in the `shop.public.customer_addresses` topic
13. `docker compose down --volumes` to clean up

—-------

## About Redpanda 

Redpanda is Apache Kafka® API-compatible. Any client that works with Kafka will work with Redpanda, but we have tested the ones listed [here](https://docs.redpanda.com/docs/reference/faq/#what-clients-do-you-recommend-to-use-with-redpanda).

* You can find our main project repo here: [Redpanda](https://github.com/redpanda-data/redpanda)
* Join the [Redpanda Community on Slack](https://redpanda.com/slack)
* [Sign up for Redpanda University](https://university.redpanda.com/) for free courses on data streaming and working with Redpanda