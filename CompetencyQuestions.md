# Competency Questions

During the development of the MOEEBIUS ontology Competency Questions (CQ) have been defined to evaluate the developed terminological knowledge model. The competency questions have been formulated in 1. and a formal implementation is provided in this document.

## CQ implemented in SPARQL

### Quickview

1. [Prefixes](#Prefixes)
2. [What are the temperature sensors in this storey?](#What-are-the-temperature-sensors-in-this-storey)

### Prefixes

The prefixes in the queries are listed below.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX brick: <https://brickschema.org/schema/1.0.3/Brick#>
PREFIX bf: <https://brickschema.org/schema/1.0.3/BrickFrame#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/vocabularies/om-2/>
PREFIX moeebius: <https://w3id.org/moeebius/MOEEBIUSOntology#>
PREFIX Abox: <https://w3id.org/moeebius/DemoSites#>
```

### What are the temperature sensors in this storey?

```sparql
PREFIX […]
SELECT ?Sensors
WHERE { 
    BIND( Abox:SI01.BL00.ST00 as ?Location )
    ?Location rdf:type brick:Floor .   
    ?Location bf:hasPart ?Room .
    ?Room bf:isLocationOf ?Sensors .
    ?Sensors rdf:type brick:Temperature_Sensor .
}
```

## Literature



1. G. D. Kontes, H. Qiu, L. Wang, M. Visser, G. F. Schneider, N. Panagiotidou, G. Grün, V. Sánchez, A. Romero-Amorrortu, P. de Agustin-Camacho, J. Rojicek, Z. Schindler, J. Malanik, A. Tsitsanis, T. Papapolyzos, C. Beder, M. Klepal, P. van Tooren, A. Nales, A. Stam, C. Malavazos, D6.8: MOEEBIUS Integrated DSS (Final Version), MOEEBIUS consortium, Brussels, Belgium (2018). |
