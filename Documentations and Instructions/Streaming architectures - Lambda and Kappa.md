Streaming architectures : 



high level viewpoint - 

message layer

data processing layer

BI/API

Batch env vs speed layer :

technology coined in 2013 - standard IOT 

Tech encounter - 

Kafka - dist message layer

streaming layer - mem cache / TimeSeries and historina / cassandra and mongo DB



batch has limitation on number of messages can be ingested - 



Kappa - =  lambda - batch ( mainly )

incomning data - darabase or data stream : 

as much possible - st

***********************

Streaming - 

deploy on data as it happens - realtime :

operating on data as it comes, has some challenges : 

with data changes - model to make correct predictions as data changes 



--lambda " 

"https://www.youtube.com/watch?v=fPlgoTLJh38&t=310s

new data - is sent to 2 places splitting and going in 2 ways - 

when new data is being streamed - we deposit into batch layer and it becomes part of master dataset

we also use to make predictions in seeed layer 

Serving ;ayer depend on the use case - 

There is website devoted : 



same data is copied into each layer - immuable master data store ( append only ) -

nothing happens - until next batch run 

when data process - we process full data - every single time and recompile views 

Speed layer - 

to make data available - 2 layers 

2once data comes here - it get processed immediately  -increment view is recompile views ( like hive )



1) batchlayer every day - evrything after wont have all data from then to next batch run 

Speed layer will help to aggregate data as it arrives, ( missing inbatch )

combine results from both layer - to get all up to date data

Serving layer - 

result of batch + speed ( indexed )

Interface of queries - 

![1547987540697](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1547987540697.png)

http://lambda-architecture.net/

![1547987008412](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1547987008412.png)



![1547987691296](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1547987691296.png)

![1547987765895](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1547987765895.png)



Kappa : 

![1547987863267](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1547987863267.png)

![1547990691679](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1547990691679.png)

![1548043415964](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1548043415964.png)

![1548043873002](C:\Users\Swagger\AppData\Roaming\Typora\typora-user-images\1548043873002.png)