# calendar-app

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

I've used vue-cli to spin this project up. I'm also using moment.js to make handling the dates a bit easier.

I've completed the original tasks and for additional features I've added the following:
- Allow events to have times, relax the one per day restriction (Note that you'll get an error if you try to submit an event at the same time as another)
- Enforce a relationship: Events can have users that can be invited (I just have a static list of users stored in App.vue)
- Display multiple view options e.g. week/month; grid with days as columns, rows as invitee/owner (I didn't get too far with this, I just added a simple Week view)
- Integrate an API (I'm using the weather API, api.openweathermap.org, to display a four day forecast for the current week)

