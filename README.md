VM101:2 
 Uncaught 
ReferenceError: process is not defined
    at 4043 (<anonymous>:2:13168)
    at r (<anonymous>:2:306599)
    at 8048 (<anonymous>:2:9496)
    at r (<anonymous>:2:306599)
    at 8641 (<anonymous>:2:1379)
    at r (<anonymous>:2:306599)
    at <anonymous>:2:315627
    at <anonymous>:2:324225
    at <anonymous>:2:324229
    at e.onload (index.js:1:1)


import React from 'react';
import routes from './routes';
import Bootstrap from 'ykb-shell';

Bootstrap(routes);



import { asyncComponent } from 'ykb-shell';

const routes = [
  {
    exact: true,
    path: '/testRoute',
    component: asyncComponent(() => import('./pages/Home')),
    i18n: lang => import(/* webpackMode: "lazy" */ `./i18n/${lang}.js`),
  },
  {
    path: '/about',
    component: asyncComponent(() => import('./pages/About')),
    i18n: lang => import(/* webpackMode: "lazy" */ `./i18n/${lang}.js`),
  },
];

export default routes;


import React from 'react';

export default function Home() {
  return <div>Home Page</div>;
}
