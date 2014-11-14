#BEM Class Name Problems

Ich gehe in diesem Dokument davon aus, dass HTML Dateien nicht gecacht werden, CSS Dateien aber schon.

##Das Problem

In den folgenden Beispielen beschreibe ich jeweils ein Module `.panel`.

    .panel {
        display:block;
    }
        .panel__inner {
            margin:0 auto;
            max-width:600px;
        }
        .panel__inner--centered {
            text-align:center;
        }
        .panel__inner--padded {
            padding:1em;
        }
        .panel__inner--red {
            background-color:red;
            color:#fff;
        }
        .panel__inner--green {
            background-color:green;
            color:#fff;
        }
        .panel__inner--yellow {
            background-color:yellow;
            
        }

Ein entsprechender HTML Code in dem dieses Module verwendet wird, sähe ähnlich dem Folgenden aus.

    <div class="panel">
        <div class="panel__inner">
            Hello World.
        </div>
    </div>

Möchte man nun dem Element `.panel__inner` des Blocks `.panel` den Modifier `.panel__inner--centered` hinzufügen, muss man die entsprechende Klasse im HTML Code ergänzen. Der Code sähe dann wie folgt aus.

    <div class="panel">
        <div class="panel__inner panel__inner--centered">
            Hello World.
        </div>
    </div>

Die Redundanz die durch `panel__inner panel__inner--centered` erzeugt wird ist wohl nicht zu übersehen. Dies erhöht die Dateigröße der HTML Datei, welche wie oben beschrieben nicht gecacht wird, durch wiederholte Verwendung enorm.  
Das Problem lässt sich durch eine minimale Anpassung im CSS Code beheben.

    .panel__inner,
    .panel__inner--centered,
    .panel__inner--padded,
    .panel__inner--red,
    .panel__inner--green,
    .panel__inner--yellow {
        margin:0 auto;
        max-width:600px;
    }

Dies ist eine verhältnismäßig geringe zusätzliche Menge an Code, die aufgrund des Browser Cachings gut in Kauf genommen werden kann. Vor allem dann wenn sich dadurch der HTML Code auf Folgendes reduzieren lässt.

    <div class="panel">
        <div class="panel__inner--centered">
            Hello World.
        </div>
    </div>

Falls man nun aber auch noch den Modifier `.panel__inner--padded` hinzufügen möchte, wächst der Code wieder.

    <div class="panel">
        <div class="panel__inner--centered panel__inner--padded">
            Hello World.
        </div>
    </div>

Auch in diesem Fall könnte man dem Problem ähnlich wie bereits zuvor getan entgegenwirken. Der angepasste Code sähe dann folgendermaßen aus.

    .panel {
        display:block;
    }
        .panel__inner,
        .panel__inner--centered,
            .panel__inner--centered--red,
            .panel__inner--centered--green,
            .panel__inner--centered--yellow,
            .panel__inner--centered--padded,
                .panel__inner--centered--padded--red,
                .panel__inner--centered--padded--green,
                .panel__inner--centered--padded--yellow,
        .panel__inner--padded,
        .panel__inner--red,
        .panel__inner--green,
        .panel__inner--yellow {
            margin:0 auto;
            max-width:600px;
        }
        
        .panel__inner--centered {
            text-align:center;
        }
            .panel__inner--centered--red {
                text-align:center;
                background-color:red;
                color:#fff;
            }
            .panel__inner--centered--green {
                text-align:center;
                background-color:green;
                color:#fff;
            }
            .panel__inner--centered--yellow {
                text-align:center;
                background-color:yellow;
            }
            
            .panel__inner--centered--padded {
                text-align:center;
                padding:1em;
            }
                .panel__inner--centered--padded--red {
                    text-align:center;
                    padding:1em;
                    background-color:red;
                    color:#fff;
                }
                .panel__inner--centered--padded--green {
                    text-align:center;
                    padding:1em;
                    background-color:green;
                    color:#fff;
                }
                .panel__inner--centered--padded--yellow {
                    text-align:center;
                    padding:1em;
                    background-color:yellow;
                }
        
        .panel__inner--padded {
            padding:1em;
        }
        
        .panel__inner--red {
            background-color:red;
            color:#fff;
        }
        .panel__inner--green {
            background-color:green;
            color:#fff;
        }
        .panel__inner--yellow {
            background-color:yellow;
        }

*Das* Problem ist dadurch behoben und die Redundanz im HTML Code könnte nun beseitigt werden.

    <div class="panel">
        <div class="panel__inner--centered--padded">
            Hello World.
        </div>
    </div>

Nun steht man allerdings vor einem anderen Problem.  
Abgesehen von der Tatsache, dass man die Anordnung der Modifier nicht einfach ändern kann und z.B. anstelle von `panel__inner--centered--padded`, `panel__inner--padded--centered` schreiben könnte, ist das eigentliche Problem die imense Zunahme des Codes in der CSS Datei, die trotz des Browser Cachings nicht tolerierbar ist. Hätte ich bspw. ein anderes Beispiel genommen und es würde Sinn machen die Modifiers `.panel__inner--red`, `.panel__inner--green` und `.panel__inner--yellow` zu kombinieren, dann würde der Code ins unermesslich wachsen.  
Die schlechte Maintenance des Codes ist übrigens kein wirkliches Problem. Diese kann unter der Verwendung eines Preprocessors sichergestellt werden.

Ein Ansatz diese Problem zu lösen könnte der folgende sein.

    .panel {
        display:block;
    }
        .panel__inner {
            margin:0 auto;
            max-width:600px;
        }
        .panel__inner[class*='--centered'] {
            text-align:center;
        }
        .panel__inner[class*='--padded'] {
            padding:1em;
        }
        .panel__inner[class*='--red'] {
            background-color:red;
            color:#fff;
        }
        .panel__inner[class*='--green'] {
            background-color:green;
            color:#fff;
        }
        .panel__inner[class*='--yellow'] {
            background-color:yellow;
        }

Dadurch nimmt der CSS Code wieder stark ab, der HTML Code könnte beibehalten werden **und** die Reihenfolge der Modifiers könnten sogar verändert werden. Trotzdem ist dies keine gute Lösung.  
Zum einen würde bspw. der Selector `.panel__inner[class*='--yellow']` auch auf den Modifier `--yellow-color` (mit dem Selector `.panel__inner[class*='--yellow-color']`) zutreffen und es würde mögliche Probleme mit der Kaskade geben.  
Das andere Problem zeigt sich wenn man annehmen würde, dass noch ein weiteres Module `.header` existiert, welches ebenfalls einen Modifier `--centered` hat. Der HTML Code würde dann z.B. folgendermaßen aussehen.

    <div class="panel">
        <div class="panel__inner header--centered">
            Hello World.
        </div>
    </div>

Der Selector `.panel__inner[class*='--centered']` würde auf dieses Element anspringen, obwohl der Modifier für `.header`, nicht aber für `.panel__inner` bestimmt war. Man könnte diese Problematik lösen wenn es in CSS etwas komplexere Attribut Selektoren gäbe, die es z.B. erlauben würden die Attribute mithilfe von RegEx's zu analysieren, doch dies bleibt uns zunächst wohl leider vorenthalten.

##Die Lösung (Ein Ansatz)

Eine Lösung, die keine der oben genannten Probleme aufwirft, aber das anfängliche Problem behebt ist folgende.

**CSS**

    [data-bem-panel] {
        display:block;
    }
        [data-bem-panel__inner] {
            margin:0 auto;
            max-width:600px;
        }
        [data-bem-panel__inner][class~='centered'] {
            text-align:center;
        }
        [data-bem-panel__inner][class~='padded'] {
            padding:1em;
        }
        [data-bem-panel__inner][class~='red'] {
            background-color:red;
            color:#fff;
        }
        [data-bem-panel__inner][class~='green'] {
            background-color:green;
            color:#fff;
        }
        [data-bem-panel__inner][class~='yellow'] {
            background-color:yellow;
        }

**HTML**

    <div data-bem-panel>
        <div data-bem-panel__inner="centered padded">
            Hello World.
        </div>
    </div>

Der HTML Code ist immernoch ziemlich kurz und die Modifiers können einfach in beliebiger Reihenfolge hinzugefügt werden.  
Der CSS Code ist ebenfalls sehr schlank. Auch das Problem, dass sich gleichnamige Modifiers von verschiedenen Modules oder Elements in die Quere kommen ist hier nicht vorhanden, da jedes Element oder Module ein eigenes Attribut benutzt.  
Der einzige Nachteil den ich derzeit an dieser Lösung ausmachen kann ist, dass z.B. der Selector `[data-bem-panel__inner][class~='yellow']` ein klein wenig spezifischer ist als `.panel__inner--yellow`, aber eine Differenz des Scores von 1 ist wohl nicht so tragisch.

##Letzte Worte

Bei Verbesserungsvorschlägen, forkt einfach diese Repo und macht einen Pull Request.  
Vielleicht kommt ja auch jemand auf die Idee dieses Dokument in eine andere Sprache zu übersetzen. In diesem Fall bitte einen Fork erstellen, den Link zur Übersetzung in dieses Dokument einfügen und einen Pull Request senden.

##jQuery Plugin

Mit [jquery.data-bem](https://github.com/futape/jquery-data-bem) ist ein jQuery Plugin verfügbar, welches die Arbeit mit BEM Klassen und `data-bem-*` Attributen komfortabel macht.

##Autor

[Lucas Krause](http://futape.de) ([@futape](https://twitter.com/futape))
