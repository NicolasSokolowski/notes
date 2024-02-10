# Leaflet

## Installation

Pour ajouter une map à une application, on utilise un module tiers : `react-leaflet` qui est construit sur `leaflet`. On installe donc les deux :

```
npm i react-leaflet leaflet
```

On importe `Leaflet` dans le fichier CSS :
```js
@import "https://unpkg.com/leaflet@1.9.4/dist/leaflet.css";
```

[React Leaflet doc](https://react-leaflet.js.org/)  
[Leaflet doc](https://leafletjs.com/examples/quick-start/)

---

On importe tous les composants de Leaflet nécessaires à l'implémentation :
```js
import { MapContainer, Marker, Popup, TileLayer } from 'react-leaflet';
```

On créé un nouvel état pour définir la position sur la map avec pour valeurs un tableau (lat et lng) :
```js
const [mapPosition, setMapPosition] = useState([40, 0]);
```
On récupère le contexte des villes :

```js
const { cities } = useCities();
```

Et on place le tout dans le return :

```js
return (
  <div className={styles.mapContainer}>
    <MapContainer 
      center={mapPosition} 
      zoom={13} 
      scrollWheelZoom={false} 
      className={styles.map}>
      <TileLayer
        attribution='&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
      />
      {cities.map((city) => (
        <Marker
          position={[city.position.lat, city.position.lng]}
          key={city.id}
        >
          <Popup>
            <span>{city.emoji}</span> <span>{city.cityName}</span>
          </Popup>
        </Marker>
      ))}
    </MapContainer>
  </div>
)
```

Il faut bien penser à donner une height au composant `MapContainer` (ici `map`) dans le fichier CSS :
```css
.map {
  height: 100%;
}
```

---
<details>
<summary></summary>
<br/>


</details>