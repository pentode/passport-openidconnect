# Passport-OpenID Connect

[Passport](https://github.com/jaredhanson/passport) strategy for authenticating
with [OpenID Connect](http://openid.net/connect/).

This module lets you authenticate using OpenID Connect in your Node.js
applications.  By plugging into Passport, OpenID Connect authentication can be
easily and unobtrusively integrated into any application or framework that
supports [Connect](http://www.senchalabs.org/connect/)-style middleware,
including [Express](http://expressjs.com/).

## Example usage

```
var express = require( 'express' );
var path = require( 'path' );
var favicon = require( 'serve-favicon' );
var logger = require( 'morgan' );
var cookieParser = require( 'cookie-parser' );
var bodyParser = require( 'body-parser' );
var session = require( 'express-session' );

var OidcStrategy = require( 'passport-openidconnect' ).Strategy;
var passport = require( 'passport' );
var ensureLogin = require( 'connect-ensure-login' );

var issuer = 'https://issuer.com/op';

var clientId = '<client id>'
var clientSecret = '<client secret>'

passport.use( new OidcStrategy( {
    issuer          : issuer,
    clientID        : clientId,
    clientSecret    : clientSecret,
    callbackURL     : '/auth/openid/return',
    scope           : 'profile openid email address',
}, function ( iss, sub, profile, verified ) {
    // 
    verified( null, profile );
} ) );

passport.serializeUser( function ( user, done ) {
    done( null, user );
} );
passport.deserializeUser( function ( user, done ) {
    done( null, user );
} );

var app = express();
app.use( session( { secret: 'pass', resave: true, saveUninitialized: true } ) );
app.use( passport.initialize() );
app.use( passport.session() );

app.get( '/', function ( req, res, next ) {
    // req.user will be available if the user has authenticated 
    // in this session
    res.json( req.user );
} );
app.get( '/account', ensureLogin.ensureLoggedIn( '/login' ), function ( req, res, next ) {
    res.json( req.user );
} );
app.get( '/login', passport.authenticate( 'openidconnect', {
    failureRedirect: '/login'
} ) );
app.use( '/auth/openid/return', passport.authenticate( 'openidconnect', {
    successReturnToOrRedirect: '/',
    failureRedirect          : '/login'
} ) );

```

## Credits

  - [Jared Hanson](http://github.com/jaredhanson)

## License

[The MIT License](http://opensource.org/licenses/MIT)

Copyright (c) 2011-2013 Jared Hanson <[http://jaredhanson.net/](http://jaredhanson.net/)>


