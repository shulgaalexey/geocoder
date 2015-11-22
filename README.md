# geocoder
Tizen Native Geocoder API Demo

Screen shots
------------

<img src="https://github.com/shulgaalexey/geocoder/blob/master/doc/create_empty_prj.png" alt="Create Empty Tizen Native Project" style="wi    dth:500px"/>

<img src="https://github.com/shulgaalexey/geocoder/blob/master/doc/set_privileges.png" alt="Set Privileges" style="wi    dth:500px"/>

Code snippets
-------------

### Add Maps Service Handle to app data

```
#include <maps_service.h>

typedef struct appdata {
	Evas_Object *win;
	Evas_Object *conform;
	Evas_Object *label;
	maps_service_h maps;
} appdata_s;
```

### Create Maps Serivice

```
/* Create Maps Service */
if(maps_service_create("HERE", &ad->maps) != MAPS_ERROR_NONE)
	return false;

/* Set Maps Provider Key */
maps_service_set_provider_key(ad->maps, "your-security-key");
```

### Release Maps Service

```
/* Release all resources. */
appdata_s *ad = data;
maps_service_destroy(ad->maps);
```


### Run Geocoder

```
/* Run Geocoder */
int request_id = 0;
maps_service_geocode(ad->maps, "Moscow Bolshoi", NULL, geocode_cb, ad, &request_id);
```

### Define Geocoder Callback

```
static bool
geocode_cb(maps_error_e result, int request_id, int index, int total,
                          maps_coordinates_h coordinates, void* user_data)
{
   double latitude = .0;
   double longitude = .0;
   maps_coordinates_get_latitude(coordinates, &latitude);
   maps_coordinates_get_longitude(coordinates, &longitude);

   char geocode[0x40] = {0};
   snprintf(geocode, 0x40, "Geocode is: (%f, %f)", latitude, longitude);

   appdata_s *ad = user_data;
   elm_object_text_set(ad->label, geocode);

   // Release the results
   maps_coordinates_destroy(coordinates);

   return true;
}
```


