### 目录结构
> root/
> build.sbt
> + src/
> ++ main/
> +++ scala/
> ++++ SprayCan.scala

### detail: 

SprayCan.scala
```scala
import akka.actor.{Props, ActorSystem, Actor}
import akka.io.IO
import akka.util.Timeout
import akka.pattern.ask
import spray.can.Http
import spray.routing.HttpService
import spray.http.MediaTypes._
import scala.concurrent.duration._

object SprayCan extends App {
	implicit val system = ActorSystem("spray-can")
	val service = system.actorOf(Props[ServicesActor], name = "service")

	implicit val timeout = Timeout(5.seconds)
	IO(Http) ? Http.Bind(service, interface = "127.0.0.1", port = 4242)
}

class ServicesActor extends Actor with Services {
	def actorRefFactory = context
	def receive = runRoute(routes)
}


trait Services extends HttpService {
	val routes =
	path("") {
		get {
			respondWithMediaType(`text/html`) {
				complete {
					<html>
						<body>
							1234
						</body>
					</html>
				}
			}
		}
	}
}

```

build.sbt
```
name := "2"

version := "1.0"

scalaVersion := "2.11.6"

scalacOptions := Seq("-unchecked", "-deprecation", "-encoding", "utf8")

libraryDependencies ++= { Seq(
"io.spray"		%% "spray-can"			% "1.3.3",
"io.spray"		%% "spray-routing"		% "1.3.3",
"com.typesafe.akka"	%% "akka-actor"			% "2.3.9"
)}

```


