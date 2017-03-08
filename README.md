## Weighted overlay application for predicting Daesh presence in Libya

### Ingest
To ingest:
```sh
> make ingest
```

Ingest process takes rasters in `data/rasters` folder, reprojects them to `EPSG:3857` and builds a raster pyramid for each image.  These pyramids are used by the tile server to provide the base tiles for weighted overlay endpoint.

The ingest process is configured by `etc/json/input-template.json` and `etl/json/output-template.json`.  The templates allow the use of environment variables to make certain configurations easier.  Notably, `$PWD` may be used to generate file URIs relative to the project checkout directory.

The ingest is based on GeoTrellis ETL utility. Full documetnation can be found [here](http://geotrellis.readthedocs.io/en/latest/tutorials/etl-tutorial/).  The included sample configuration outputs the pyramid to S3; however other options, such as the local file system, are supported.

For raster sets too large to be processed on a computer this process may be run on a cluster.

## Tile Server

To run the server locally, through SBT:

```sh
> make run
```

### Cloud Foundry Deployment

Another option is to publish to Pivotal Cloud Foundry.  This requires building a universal package for distribution:

```sh
> make universal
```

Once that succeeds, one may push the result via `cf push`.

### Docker images

To build and run the Docker image suitable for deployment:

```sh
> make docker-build
> make docker-run
```

The docker image name and path can be customized through `${IMG}` `${TAG}` variables in the `Makefile`

Given layers which are small in size and at low resolution the catalogs can be fully included in the docker image and file system URIs may be used in the `input-template.json` (using the `hadoop` type in the backend specification).  If larger layers were to be used they would need to be stored on a remote service such as Amazon S3.
