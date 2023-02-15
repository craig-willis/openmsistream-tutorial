# openmsistream-tutorial
Simple setup to run through the openmsistream tutorial

[OpenMSIStream tutorial](https://openmsistream.readthedocs.io/en/latest/introduction/tutorials.html)


# Build the container image

```
cd docker
docker build -t <user>/openmsistream .
```

or use image `craigwillis/openmsistream`

# Create the broker

`docker-compose up`

# Create the topic

In a seperate terminal:
```
docker exec -it broker kafka-topics \
   --bootstrap-server broker:9092 \
   --command-config /etc/kafka/topics_config.properties \
   --create --topic openmsistream_tutorial_data
````

# Test data

Test data has already been downloadedin the `data` folder:

```
cd data
wget https://data.paradim.org/194/XRD/SC001/SC001%20XRR.csv
wget https://data.paradim.org/194/XRD/SC002/SC002%20XRR.csv
wget https://data.paradim.org/194/XRD/SC006/SC006%20XRR.csv
wget https://data.paradim.org/194/XRD/SC007/SC007%20XRR.csv
wget https://data.paradim.org/194/XRD/SC009/SC009%20XRR.csv
```

# Start the OpenMSIStream producer
```
docker run --name openmsistream \
    --net host -it -v `pwd`:/example craigwillis/openmsistream bash

mkdir openmsistream_upload

DataFileUploadDirectory --config test.config openmsistream_upload     --topic_name openmsistream_tutorial_data 
```

# Run OpenMSIStream consumer

```
docker exec -it openmsistream bash

DataFileDownloadDirectory --config test.config openmsistream_download --topic_name openmsistream_tutorial_data 
```

Copy data to the `openmsistream_upload` directory and confirm that it appears in the 
download directory:
```
cp "data/SC001 XRR.csv" openmsistream_upload
ls openmsistream_download
```

Run XRD CSV Plotter in openmsistream container and confirm that the plot appears in the 
output directory.
```
python -m xrd_csv_plotter --topic_name openmsistream_tutorial_data

ls XRDCSVPlotter_output
```
