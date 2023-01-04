## Steps to prepare the ETOPO2 data set <a name="etopo2"></a>
We use the ETOPO2 version although there are newer version of the data set. The ETOPO2 dataset contains topographic information for the entire surface of the Earth (both above and below sea level) at a nominal resolution of two arc-minutes (~4 km). 

First fetch the zip file and unzip it:
```
wget http://www.ngdc.noaa.gov/mgg/global/relief/ETOPO2/ETOPO2v2-2006/ETOPO2v2c/raw_binary/ETOPO2v2c_i2_LSB.zip
unzip ETOPO2v2c_i2_LSB.zip
```
This is a binary file with a format described in the `ETOPO2v2c_i2_LSB.hdr` file. Create a file `etopo2xyz.c` containing the following code:
```
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>
#include <arpa/inet.h>
 
/* check that this matches ETOPO2v2c_i2_LSB.hdr */
#define NCOLS 10800
#define NROWS 5400
#define XLLCORNER -180.000000
#define YLLCORNER -90.000000
#define CELLSIZE 0.03333333333
#define NODATA_VALUE -32768
#define BYTEORDER LSBFIRST
#define NUMBERTYPE 2_BYTE_INTEGER
#define MIN_VALUE -10791
#define MAX_VALUE 8440
 
int main (int argc, char * argv[])
{
  double lat, lon;
  int16_t v;
  int i, j;
 
  for (j = 0; j < NROWS; j++) {
    lat = YLLCORNER + CELLSIZE*j;
    for (i = 0; i < NCOLS; i++) {
      lon = XLLCORNER + CELLSIZE*i;
      assert (fread (&v, sizeof (int16_t), 1, stdin));
      assert (v >= MIN_VALUE && v <= MAX_VALUE);
      printf ("%.8f %.8f %d\n", lon + CELLSIZE/2., - (lat + CELLSIZE/2.), v);
    }
    fprintf (stderr, "\rRow %d/%d              ", j + 1, NROWS);
  }
  fputc ('\n', stderr);
  return 0;
}
```
Compile the c code: </br>
`cc etopo2xyz.c -o etopo2xyz`

The following command will then read the binary ETOPO2 file, convert it to the appropriate text format 
and generate the final terrain database (better run it using slurm): </br>
`./etopo2xyz < ETOPO2v2c_i2_LSB.bin | xyz2kdt -v etopo2`

If everything went well we should have the following files `etopo2.kdt etopo2.pts etopo2.sum`, they TOGETHER define the terrain. 
We only need to put them together under a directory `./terrain` under where the executable `tsunami` is. 
This line in the source code will find the files that match the etopo2 name under that folder: </br>
`terrain (zb, "~/terrain/etopo2", NULL)`

## Steps to prepare the SRTM data set 
There seems to be no good way of downloading the SRTM data. The official site is unclear and requires authentication. I found the data from [this unoffical website](https://srtm.kurviger.de/SRTM3/Eurasia/index.html). Maybe try [Earth data](https://www.earthdata.nasa.gov/)? Or [this site](https://www.usna.edu/Users/oceano/pguth/md_help/html/srtm.htm)?

In any case, first find a way to download all the files in the `/SRTM3/Eurasia/` folder. This may take more than an hour. (This bash command `wget -r -l1 -A 'S*.zip' https://srtm.kurviger.de/SRTM3/Eurasia/ ./srtm.kurviger.de/SRTM3/Eurasia/` was helpful when I experienced internet interruption.)

Similarly in the directory where the `srtm.kurviger.de` folder is located, create a file named `srtm2kdt.c`:

```
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>
#include <glib.h>
 
#define NCOLS 1201
#define NROWS 1201
#define CELLSIZE (1./(NCOLS - 1))
#define NODATA_VALUE -32768
 
int main (int argc, char * argv[])
{
  double lat, lon;
  gint16 v, vs;
  int i, j;
 
  double xllcorner = atof (argv[1]);
  double yllcorner = atof (argv[2]);
 
  for (j = 0; j < NROWS; j++) {
    lat = yllcorner + CELLSIZE*(NROWS - 1 - j);
    for (i = 0; i < NCOLS; i++) {
      lon = xllcorner + CELLSIZE*i;
      assert (fread (&v, sizeof (gint16), 1, stdin));
      vs = GINT16_FROM_BE (v);
      if (vs > 0)
	printf ("%.8f %.8f %d\n", lon, lat, vs);
    }
    //    fprintf (stderr, "\rRow %d/%d              ", j + 1, NROWS);
  }
  //  fputc ('\n', stderr);
  return 0;
}
```
and compile using `cc 'pkg-config glib-2.0 --cflags --libs' -Wall -O2 srtm2kdt.c -o srtm2kdt` (notice the backtick). Then run the following bash script.

```
for f in srtm.kurviger.de/SRTM3/Eurasia/*.hgt.zip; do
    p=`echo $f | sed 's/.*\([NS]\)\([0-9][0-9]\)\([WE]\)\([0-9][0-9][0-9]\)\.hgt\.zip/\1 \2 \3 \4/'`
    lat=`echo $p | awk '{if ($1 == "S") print -$2; else print $2;}'`
    lon=`echo $p | awk '{if ($3 == "W") print -$4; else print $4;}'`
    echo $f >> /dev/stderr
    unzip -c -q $f | ./srtm2kdt $lon $lat
done | xyz2kdt -v srtm_eurasia 
```

This script loops through the latitude and longitude to construct the end terrain file `srtm_eurasia`. This took about four hours on the tiger cluster (1 core) in my case. I could not run this script on my personal laptop due to memory constraints.

PS: a useful website [explainshell](https://explainshell.com/) to decipher shell script.
