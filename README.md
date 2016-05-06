# passport-trademe

[Trade Me](http://www.trademe.co.nz) authentication strategy for [Passport](http://passportjs.org/).

## Installation

### Installing passport-trademe
```
  [sudo] npm install passport-trademe
  
  // or for this updated FORK install like this:
  // where 1.0.0.1 = release number. Look for the latest release from above
  npm install https://github.com/tealdit/passport-trademe/archive/1.0.0.1.tar.gz --save
  
```

## Motivation
Fame, fortune ... free beer.  

## Usage

Very simple ...

``` js
	var passport = require( 'passport' )		
		, TrademeStrategy = require( 'passport-trademe' ).Strategy
		
	...
	
	var trademeConfig = require( './keys/trademeConfig' );
	passport.use(new TrademeStrategy( {
		consumerKey: trademeConfig.consumerKey
		, consumerSecret: trademeConfig.consumerSecret
		, callbackURL: app.get( 'site-url' ) + '/auth/trademe/callback'
	}
	, function( token, tokenSecret, profile, done ) {
	
		...
		
		}
	) );
	
	...
	
	app.configure( function() {
		app.use( passport.initialize() );
		app.use( passport.session() );
		app.use( app.router );
	} );
```

### Content of ./keys/trademeConfig.js
```js
/**
 * Expose constructors.
 */
var apiBaseUrl      = 'https://api.trademe.co.nz/v1/';
var oAuthBaseUrl    = 'https://secure.trademe.co.nz/Oauth/';

var env = 'staging';
switch (env){
    case 'staging':
        apiBaseUrl      = 'https://api.tmsandbox.co.nz/v1/';
        oAuthBaseUrl    = 'https://secure.tmsandbox.co.nz/Oauth/';
        break;
    default:
}
module.exports = {
    consumerKey     : 'E11122CC33C3F159ADF1CB1B6217',
    consumerSecret  : 'F33456645479B111B44386EC085A7C',
    apiBaseUrl      : apiBaseUrl,
    oAuthBaseUrl    : oAuthBaseUrl,
    callbackURL     : 'http://127.0.0.1:3000/auth/trademe/callback'
};
```

Define you "conversation" routes ...

```js
	app.get( '/auth/trademe'
		, passport.authenticate( 'trademe'
			, { 
				// Add the scope of your application here
				scope: [ 'MyTradeMeRead'
					, 'MyTradeMeWrite'
					, 'BiddingAndBuying' ] 
			} 
		)
		, function( req, res ) {
			// Nothing to do here. The "conversation" will end at the callback route.
		} );
	
	app.get( '/auth/trademe/callback' 
		, passport.authenticate( 'trademe' 
			, {
				failureRedirect: '/login'
			}
		)
		, function( req, res ) {
			res.redirect( '/' );
		} );
```

Use to protect something ...

```js
	function ensureAuthenicated = function( req, res, next ) {
		if ( req.isAuthenticated() ) { 
			return next(); 
		}
		res.redirect( '/login' )
	}
	
	...
	
	app.get( '/someProtectedResource'
		, ensureAuthenticated
		, function ( req, res ) {
			res.redirect( '/' );
		} );
```

## Authors: 

* [Cleave Pokotea](https://github.com/Tumunu)
* [Damodar Bashyal](http://dltr.org)
