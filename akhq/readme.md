# some example configs for running akhq


## basic
one broker nothing else --> [ basic akhq](basic_akhq.yml)
adapt hostname accordingly

docker run --network=host -d  \
    -p 8080:8080 \
    -v ./basic_akhq.yml:/app/application.yml \
    tchiotludo/akhq 
