# place-names
A (historical) place name dictionary

The purpose of this project is to maintain a dictionary that could be used in different 
other projects where you need to find normalized name forms of historical place names, or 
simply add geoordinates to modern place names.

# usage
## `data/place-synonyms-normalized.csv` contains a dictionary.

It has the following columns: 
- `original` contains a name form found in sources (in my case biblographical records), 
- `normalized` contains the current name of the location (same as the `city` column in `ccord.csv`), 
- `factor` is floating point number denoting if the Geoname covers a part of the name (sometimes the original phrase contains several different locations).

an example
```csv
Altenbvrgi,Altenburg,1
```
Here `factor` is 1, so it fits to one particular location.

```cav
"Altonaviae [Altona] et Servestae [Zerbst]",Altona,0.5
"Altonaviae [Altona] et Servestae [Zerbst]",Zerbst,0.5
```
Here the same phrase denotes two distinct location, so each gets 0.5 factor.

Depending on your use case, you may or may not utilize factor. Note: this is a generated file, please do not edit directly, see the maintenance section.

## `coord.csv` contains the Geoname entries. It has the following columns:
- `city`: a current name of the location (same as the `normalized` in `place-synonyms-normalized.csv` and `place-synonyms.csv`)
- `geoid`: the Geoname identifier
- `name`: the name in Geoname
- `country`: current country
- `lat`: latitude
- `long`: longitude

# maintenance

The file `data_internal/place-synonym.csv` is the file that we use to build the dictionary. It has the following format:

```
<current name>=<historical name>|<historical name>|<historical name>|...
```
where 
- `<current name>` is the same as `city` in coord.csv, and `normalized` in place-synonyms-normalized.csv
- `<historical name>` is a historical name format or a language variation, same as `original` in place-synonyms-normalized.csv

An example:
```
Altenburg=Altenburgi|[Altenburg]|Altenburgi [Altenburg]|Altenbvrgi
```

If the name is displayed in a complex phrase together with multiple other locations, we create distinct entries for such phrases.

An example:
```
Altona=[Altona]|Altonae|Altonaviae
# multi
Altona|Zerbst=Altonaviae [Altona] et Servestae [Zerbst]
Altona|Flensburg=Altona und Flensburg
```

When the file's edition is finished, the normalized version should be generated with

```bash
php scripts/normalize-synonyms.php
```

To add new entries to coords, you should have Geoname APU key, and place into a `secret.sh` file:

```bash
cp secret.template.sh secret.sh
# add your API key to USER variable (USER=<API key>)
```

The respository has two helper scripts: `scripts/geoname.sh` and `scripts/geocode-cities.sh`.

```bash
scripts/geoname.sh Altenburg
```
Returns information from Geoname. You can add this line (removing the last element) to coords.csv

You can run it on multiple locations by adding locations to `data_internal/city-by-works.csv` file (each entry into a distinct line), then run 

```bash
scripts/geocode-cities.sh
```
