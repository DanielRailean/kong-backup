docker run -d --network="host" --name kong \
    -e "KONG_DATABASE=postgres" \
    -e "KONG_PG_HOST=localhost" \
    -e "KONG_PG_PORT=5432" \
    -e "KONG_PG_USER=postgres" \
    -e "KONG_PG_PASSWORD=pass" \
    -e "KONG_MIGRATE=yes" \
    -e "KONG_ROLE=control_plane" \
    -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
    -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
    -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
    -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
    -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
    -e "KONG_PLUGINS=bundled" \
    -e "KONG_ERROR_DEFAULT_TYPE=application/json" \
    -e "KONG_CLUSTER_CERT=/certs/cluster.crt" \
    -e "KONG_CLUSTER_CERT_KEY=/certs/cluster.key" \
    -e "KONG_LUA_SSL_TRUSTED_CERTIFICATE=/certs/cluster.crt" \
    -v /path/to/repo/kong-backup/cluster.key:/certs/cluster.key \
    -v /path/to/repo/kong-backup/cluster.crt:/certs/cluster.crt \
    bitnami/kong

docker run -d --network="host" --name kong-dp \
    -e "KONG_DATABASE=off" \
    -e "KONG_ROLE=data_plane" \
    -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
    -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
    -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
    -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
    -e "KONG_PLUGINS=bundled" \
    -e "KONG_ERROR_DEFAULT_TYPE=application/json" \
    -e "KONG_CLUSTER_CERT=/certs/cluster.crt" \
    -e "KONG_CLUSTER_CERT_KEY=/certs/cluster.key" \
    -e "KONG_LUA_SSL_TRUSTED_CERTIFICATE=/certs/cluster.crt" \
    -e "KONG_CLUSTER_CONTROL_PLANE=localhost:8005" \
    -v /path/to/repo/kong-backup/data_plane.lua:/opt/bitnami/kong/openresty/share/lua/5.1/kong/clustering/data_plane.lua \
    -v /path/to/repo/kong-backup/cluster.key:/certs/cluster.key \
    -v /path/to/repo/kong-backup/cluster.crt:/certs/cluster.crt \
    -v /path/to/repo/kong-backup/config.json:/opt/bitnami/backup/config.json \
    bitnami/kong