# gathertown alarms 

Experiment with the [gathertown](https://gather.town) [websockets API](https://gathertown.notion.site/Gather-Websocket-API-bf2d5d4526db412590c3579c36141063), which is under heavy development at the moment so this is probably already deprecated. 

This is more for reference purposes than actual function (though it's still totes functional). Configuration is all in code as described below: 
## <img src='https://user-images.githubusercontent.com/93146809/146673547-0204dbe5-28b4-432d-9d6d-44c97f5c5f16.png' width=45 height=30>  config.ts 
Configuration for your space is controlled in config.ts, and should look something like this. 
```typescript
export const API_KEY  = "YOUR-KEY-HERE"
export const SPACE_ID   = "OoOoOaAaAaAhHhH\\My-Gather-Space"
export const MAP_ID = "room-id-from-map-editor"
```
*  Go [here](https://gather.town/apiKeys) to get an API key
*  The space ID you extract from the URL of your gathertown. So if your space is `https://gather.town/app/OoOoOaAaAaAhHhH/My-Gather-Space` the map ID will be `OoOoOaAaAaAhHhH\\My-GatherSpace`. Note that the `/` seperating the ID string from the name is replaced with `\\`
*  The Map ID is the name of the specific room, you can get it from opening up the Mapmaker page, clicking the objects button, and selecting the Rooms tab on the right-hand panel. 
##  <img src='https://user-images.githubusercontent.com/93146809/146673336-6196731e-099c-497c-9fc6-6ea4ba74b34a.png' width=45 height=30>  Setup fixtures in Templates.ts png) 
The fixtures (objects) that you can flash are described in `lib/Templates.ts` :  
```typescript
    data:TemplateData = {  
        'Neon Light (Circle)': {  colors: {  indigo: '0-8Rb8J_0GNvdAmWQautc',  opal: 'uMGrU7RzdguwhPy2Ymbgq', etc: '...' }  },  
        'Neon Light (Vertical)': {  colors: {  indigo: 'D-rmjVPJMfGfTD_uytCSH',  opal: '8UXWGRuSBgxgoF3SWGn0F',  etc: '...'  }  },  
        'Cyberpunk Streetlamp': {  colors: {  indigo: '_1x4AjD5IINB2xYi10sz-',  opal: 'zmEKs_R0f7nJnBChYqCdh',  etc: '...' }  },  
        'Neon Light (Hexagonal) (3)': {  colors: {  hot_pink: 'w8BXJhxPd6LjeuR7sEoZN', etc: '...'}  },  
        'Neon Light (Heart)': {  colors: {  indigo: 'pEDurMYS7UloO8HpwsE0F',  opal: 'XsXSU2UHsuV0BCJFQ2AcV', etc: '...' }  }, 
        'Neon Sign (Pirate)': {  colors: {   lemon: 'hk2F0UZZIILSQCeIL07l5',  hot_pink: 'JBd6Jpb-eimRsmefO6l2W', etc: '...'  }  }, 
        'Neon Light (Floor)': {  colors: {   ivory: 'x4kUNlkyKX9qaL3fxOGD6',  carrot_orange: 'tzsoZlPKC7mt5LVaBeJW_',  etc: '...'  }  },  
        'Neon Light (Hexagonal) (7)': {  colors: {   opal: 'xmtcO71mvx4OdAYoaoF2P',  alpine: 'Rq3bazWkQYdAFlys-B3X_',  etc: '...'}  }  
    }
```
The default set contains all the available colors for those objects. You can generate new entries for this file from your map by running the print utility with `npm run print`. This will save a copy of your map to a map.json file in the root directory, and print out a TemplateData object containing every object in your map to the console. You can edit this and drop it directly into place over whatever is currently in TemplateData. 

Color names are generated by (ntc-ts code)[https://github.com/Danetag/ntc-ts] I copied directly over into the project as the installation appeared to be broken via npm. 

## <img src='https://user-images.githubusercontent.com/93146809/146673555-473bfd1e-b765-4dfc-954f-8b749214d02e.png' width=45 height=30>   Configure the filters 
The objects which are modified can be controlled by passing an anonymous function suitable which will be run on each individual `MapObject`, returning true if that object should be included. The filter will be bound to the List class, so you can utilize anything that it touches, and is added via the `registerFilter` method on the List class. The default is to filter out any objects that don't exist in the Templates.data definitions : 
```typescript
    (object:MapObject) => this.templates.names.includes(object._name!)
```

## <img src='https://user-images.githubusercontent.com/93146809/146673558-daf36702-abd8-46f3-afc4-e660ec6c5efc.png' width=45 height=30>  Configure the alarm 
Alarms are also configured via anonymous functions through the instantiation of an Alarm object, along with an interval time indicating how often the alarm check logic should be run. The test should return false when the alarm is tripped, and true when it is not. =THe default alarm is always in an alerting phase : 

```typescript
    let alarm =new Alarm('AlwaysOn', (() => false))
    list.registerAlarm(alarm, 1000)
```
