
#Geoip filter plugin

#make ssl cer :
cd ssl
./ssl-gen.sh

#get ssl cert subject 
openssl x509 -subject -nameopt RFC2253 -noout -in node1.pem
openssl x509 -subject -nameopt RFC2253 -noout -in node2.pem
openssl x509 -subject -nameopt RFC2253 -noout -in node3.pem
openssl x509 -subject -nameopt RFC2253 -noout -in admin.pem

cd ..

#make volume in docker coompose file:

docker compose up -d

#and 

docker compose down




#copy config to volume
cp opensearch-1.yml /var/lib/docker/volumes/geoip_opensearch-data1-config/_data/config/opensearch.yml 
cp opensearch-2.yml /var/lib/docker/volumes/geoip_opensearch-data2-config/_data/config/opensearch.yml 
cp opensearch-3.yml /var/lib/docker/volumes/multiple-pipelines_opensearch-data3-config/_data/config/opensearch.yml

#copy cert to volume
cd ssl
cp *.pem /var/lib/docker/volumes/geoip_opensearch-data1-config/_data/config/
cp *.pem /var/lib/docker/volumes/geoip_opensearch-data2-config/_data/config/
cp *.pem /var/lib/docker/volumes/geoip_opensearch-data3-config/_data/config/



#change owner  config dir
chown -R 1000.1000 /var/lib/docker/volumes/geoip_opensearch-data1-config/_data/config/
chown -R 1000.1000 /var/lib/docker/volumes/geoip_opensearch-data2-config/_data/config/
chown -R 1000.1000 /var/lib/docker/volumes/geoip_opensearch-data3-config/_data/config/
chown -R 1000.1000 /var/lib/docker/volumes/geoip_file-log/_data/

#active cert in container
docker exec -it ${containername} bash
#then copy this code for active cert :

bash plugins/opensearch-security/tools/securityadmin.sh 
-icl -nhnv -cacert config/root-ca.pem -cert config/admin.pem -key config/admin-key.pem

#curl test cluster response
curl -XGET --insecure -u 'admin:admin' https://localhost:9200/_cluster/health
curl -XGET --insecure -u 'admin:admin' https://localhost:9200/_cluster/health?pretty


#get ssl cert subject 
openssl x509 -subject -nameopt RFC2253 -noout -in node1.pem
openssl x509 -subject -nameopt RFC2253 -noout -in node2.pem
openssl x509 -subject -nameopt RFC2253 -noout -in node3.pem
openssl x509 -subject -nameopt RFC2253 -noout -in admin.pem





