# Reactstrap Sample

This repository is an instructional example for creating a Rails application
using Webpacker, React.js and Reacstrap. It's based on Rails 5.2.2 running on
Ruby 2.5.1. The master branch of the this repository represents the end state
of all the tutorial steps documented in this README file.

# Setup

To setup this application we did the following which includes installation and
updating of the javascript packages:

1. `rails new reactstrap-rails --webpack=react --skip-coffee --skip-javascript`
1. `cd reactstrap-rails`
1. `yarn upgrade @rails/webpacker@^4.0`
1. `yarn upgrade webpack-dev-server@^3.2`
1. `yarn add reactstrap`
1. `yarn add bootstrap@^4.3.1`
1. `yarn check` - should not yield any warnings

# Initial Controller and View Setup

1. Edit Gemfile to down grade Sqlite3 from 1.4.x since Rails doesn't support it yet.
   ```
   gem 'sqlite3', '~> 1.3.0'
   bundle update
   ```
1.  Create the home controller.
   ```
   rails g controller Home index
   ```
1. Add the root route to the config/routes.rb file.
   ```
   root to: 'home#index'
   ```
1. Update the view template app/views/layouts/application.html.erb by adding:
   ```
   <%= javascript_pack_tag 'hello_react' %>
   ```
1. Update config/initializers/content_security_policy.rb to allow content from
   webpack-dev-server.
   ```
   policy.connect_src :self, :https, 'http://localhost:3035', 'ws://localhost:3035' if Rails.env.development?
   ```
The above steps should get you a simple hello world example up and running. We
will move on to something a little more complex in the next section.

# Adding Bootstrap Components

In this section we modify our sample application to use a couple of React.js
components that are part of the Reactstrap package. The steps for this
modification are:

1. Remove the old webpack entry point for application the
   `<%= javascript_pack_tag 'hello_react' %>` entry and replace it with our
   new entry point and style sheet in the `<head>` section:
   ```
   <%= stylesheet_link_tag 'application', media: 'all' %>
   <%= javascript_pack_tag 'application' %>
   ```
1. Update the app/javascript/packs/application.js file to include the lines
   below. The console messages are just there for debugging to make sure
   the entrypoint is being loaded:
   ```
   console.log('Hello World from Webpacker')
   import 'bootstrap/dist/css/bootstrap.min.css'
   console.log('Finished inmporting bootstrap')
   ```
1. Create a new directories for the Reactstrap components we plan on adding to
   our little application. The structure should look like, but do not worry
   about the additional files, we will be creating those soon.
   ```
   app/javascript
   ├── example_jumbotron
   │   ├── components
   │   │   └── example_jumbotron.jsx
   │   └── index.js
   ├── example_nav
   │   ├── components
   │   │   └── example_nav.jsx
   │   └── index.js
   └── packs
       ├── application.js
       ├── components.js
       └── hello_react.jsx
   ```
1. Create the `example_jumbotron.jsx` file, which is really just the example
   code from the Reactstrap documentation:
   ```
   import React from 'react';
   import { Jumbotron, Button} from 'reactstrap';

   const ExampleJumbo = (props) => {
     return (
       <div>
         <Jumbotron>
           <h1 className="display-3">Hello, world!</h1>
           <p className="lead">This is a simple hero unit, a simple Jumbotron-style component for calling extra attention to featured content or information.</p>
           <hr className="my-2" />
           <p>It uses utility classes for typography and spacing to space content out within the larger container.</p>
           <p className="lead">
             <Button color="primary">Learn More</Button>
           </p>
         </Jumbotron>
       </div>
     );
   };

   export default ExampleJumbo;
   ```
1. Create the `app/javascript/example_jumbotron/index.js`, which just includes
   all thecomponent files. In this case, we only have one component for the
   jumbotron. Notice the *id* selector, we will use that in our Rails view.
   ```
   import React from 'react'
   import ReactDOM from 'react-dom'
   import ExampleJumbo from './components/example_jumbotron'

   const jumbotron = document.querySelector('#jumbotron')
   ReactDOM.render(<ExampleJumbo />, jumbotron)
   ```
1. Create the `app/javascript/packs/components.js`,which is an import manifest
   for the components that we are using in our application.
   ```
   import 'example_jumbotron'
   import 'example_nav'
   ```
1. Create `app/javascript/example_nav/components/example_nav.jsx`, which is
   based on the Reactstrap example for the basic navigation bar.
   ```
   import React from 'react';
   import {
     Collapse,
     Navbar,
     NavbarToggler,
     NavbarBrand,
     Nav,
     NavItem,
     NavLink,
     UncontrolledDropdown,
     DropdownToggle,
     DropdownMenu,
     DropdownItem } from 'reactstrap';

   export default class ExampleNav extends React.Component {
     constructor(props) {
       super(props);

       this.toggle = this.toggle.bind(this);
       this.state = {
         isOpen: false
       };
     }
     toggle() {
       this.setState({
         isOpen: !this.state.isOpen
       });
     }
     render() {
       return (
         <div>
           <Navbar color="light" light expand="md">
             <NavbarBrand href="/">reactstrap</NavbarBrand>
             <NavbarToggler onClick={this.toggle} />
             <Collapse isOpen={this.state.isOpen} navbar>
               <Nav className="ml-auto" navbar>
                 <NavItem>
                   <NavLink href="/components/">Components</NavLink>
                 </NavItem>
                 <NavItem>
                   <NavLink href="https://github.com/reactstrap/reactstrap">GitHub</NavLink>
                 </NavItem>
                 <UncontrolledDropdown nav inNavbar>
                   <DropdownToggle nav caret>
                     Options
                   </DropdownToggle>
                   <DropdownMenu right>
                     <DropdownItem>
                       Option 1
                     </DropdownItem>
                     <DropdownItem>
                       Option 2
                     </DropdownItem>
                     <DropdownItem divider />
                     <DropdownItem>
                       Reset
                     </DropdownItem>
                   </DropdownMenu>
                 </UncontrolledDropdown>
               </Nav>
             </Collapse>
           </Navbar>
         </div>
       );
     }
   }
   ```
1. Create the `app/javascript/example_nav/index.js` file to render the
   the navigation.
   ```
   import React from 'react'
   import ReactDOM from 'react-dom'
   import ExampleNav from './components/example_nav'

   const navbar = document.querySelector('#navbar')
   ReactDOM.render(<ExampleNav />, navbar)
   ```
1. Update the `app/views/layouts/application.html.erb` to include the
   components and the navigation bar:
   ```
   <body>
     <div id="navbar"></div>
     <%= yield %>
     <%= javascript_pack_tag 'components' %>
   </body>
   ```
1. Update the `app/views/home/index.html.erb` to include the jumbotron
   component.
   ```
   <div id="jumbotron"></div>
   <h1>Home#index</h1>
   <p>Find me in app/views/home/index.html.erb</p>
   ```
