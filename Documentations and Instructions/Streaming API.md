# Streaming API



CryptoCurrency API : 

https://min-api.cryptocompare.com/documentation?key=Historical&cat=dataHistoday

https://github.com/jeffreytai/cryptocompare-java-api-wrapper

```java
import com.crypto.cryptocompare.api.CryptoCompareApi;
import com.google.gson.JsonObject;

//https://github.com/jeffreytai/cryptocompare-java-api-wrapper
// key https://www.cryptocompare.com/cryptopian/api-keys
// rodeorajputs@outlook.com
// f73dca7e0e8e11a1e6829686eb2210kiara7d22eb3d6d302222ee36c5a2dbe5cc02dakiara
//https://min-api.cryptocompare.com/documentation?key=Historical&cat=dataHistoday

object cryptoComapreApi extends App{

  val api = new CryptoCompareApi();
  val response = api.coinList();
  println(response.getAsJsonObject())
      
  //val histResponse = api.histoHour("POS","POS", java.util.Map["a" : "b"])

}
```



Yahoo finance: 

https://financequotes-api.com/#singlestock

```
libraryDependencies += "com.yahoofinance-api" % "YahooFinanceAPI" % "3.14.0"
```

sample code : 

```scala
import java.io.IOException
import yahoofinance.Stock
import yahoofinance.YahooFinance
import yahoofinance.quotes.stock.StockQuote
import yahoofinance.histquotes.Interval
import java.util.Calendar

object yahooFinanceMultipleHist {

  def main(args: Array[String]): Unit = {

    val symbols =  Array[String] {
      "INTC"
      "BABA"
      "TSLA"
      "AIR.PA"
      "YHOO" }

    //val stocks = YahooFinance.get(symbols);

    val from = Calendar.getInstance
    val to = Calendar.getInstance
    from.add(Calendar.YEAR, -5) // from 5 years ago


    val google = YahooFinance.get("GOOG", from, to, Interval.WEEKLY)
    println(google)

    val tesla = YahooFinance.get("TSLA", true);
    println(tesla.getHistory())

     // Thread.sleep(1000)
  }
}
```



**Local Streaming simulation using mobaXterm**

Netcat alternative: 

<https://mobaxterm.mobatek.net/download-home-edition.html>

```shell
nc -l 9999 -i 1 <   accessLog.txt

## command for mobaXterm
while read -r line ; do echo "$line" | nc -l 9999 ; sleep 0.001; done < "streaming_sales.csv"
```

