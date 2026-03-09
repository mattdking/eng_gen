Create a web application that generates synthetic engagement data. Users can create one or more Demo Collections (Demos) that contain definitions needed to create the engagement data. 

The Synthetic Marketing Engagement Data Generator is a tool designed to create realistic, simulated marketing activity data for demo environments. The system allows users to define complex customer journeys (Flows) within Campaigns and generate time-series engagement data that can be piped into multiple destination environments.

Hierarchy:  
* Demo Collection(s): A demo collection is a collection of assets for the target account type (Retail, Commercial Banking, Personal Banking, etc). The fields that need to be tracked are: Collection Name
   * Persona(s): Users start by building target personas that are relevant for our collection. For example, a demo for a Retail collection might have the following personas: Eco-Warrior, Bargain Hunter, Brand-Loyalist, Innovators). These will later be processed by the Profile Engine to generate thousands of unique individuals.
      * Data: Each persona is defined by a set of data points.
         * Engagement propensity shift: a persona set might be more or less likely to engage with content. Users should be able to define the "shift" for each persona.
            * Open rate shift
            * Click rate shift
            * Conversion rate shift
            * [Suggest other types of engagement]
         * Object types:
            * Individual object: This is data directly tied to them as individuals. Required fields:
               * First Name: The first name of the golden record. Profiles generated from this record should be generated at random using Faker or similar.
               * Last Name: The last name of the golden record. Profiles generated from this record should be generated at random using Faker or similar.
               * Full Name: Concat First Name + " " + Last Name.
               * Phone Number: I want a way to generate localized synthetic phone numbers based on some input. Give me ideas.
               * Email Address: Concat, lowercase of First Name + "." + Last Name + "@example.com"
               * Individual ID: Run the other fields through base-62 encoding to product an 18-character string.
            * Related objects: Data that has a many-to-one relationship with an individual. Examples of related objects for a retail use case include  but aren't limited to: 
               * Website Engagement history
               * Website Search history
               * Purchase history
               * Case history
            * Importing data:  The UI should have a way to paste in a json file that defines the fields on the Individual object as well as related objects and fields. For each data point, users should be able to define the following via the json file:
               * A set of possible values to populate to a field with a probability for each
               * A way to map field values dependent to another field value
               * A way to set multiple possible values on that mapping or a range for numeric or dates
               * A way to set T-minus and T-plus logic (the date should be x days before or after a given date).
   * Campaign(s): Define the campaigns within the demo.
      * Flow(s): Define the flows within the campaign.
         * Flow Element(s): Define the flow elements within the flow. Flows contain branching logic. Flow element examples:
            * Flow Trigger: The event or activity that enters individuals into a flow. There can only be one Flow Trigger per flow. Trigger types:
               * Segment Trigger
               * Event Trigger
               * Form Trigger
            * Email message
            * SMS message
            * WhatsApp message
            * Wait: Sets a period of time (hours, days, weeks, months) that an individual should virtually wait before moving to the next step.
            * Split: Defines a route that individuals follow down a flow. Contains 2 or more branches. 
               * Branch: Each branch defines a percentage of the audience that should flow down each path of the split.  The sum of the branches should add up to 100%. Ideally there is a simple way in the UI to manage these split percentages so users don't have to worry about doing too much math. Either auto-adjusting the numbers to total 100% onBlur or providing some type of slider.
            * Route to Flow: Sends Individuals to another flow.
         * Conversion Steps: Each flow has a goal, to get users to make a purchase, view a webpage, sign up for an event, etc. Conversion elements are the steps that could be taken after a user clicks from a message. Users should be able to define a series of conversion steps that could be triggered any time a user clicks from an email. A user would define the probability that someone who lands on a step advances to the next step. For simplicity there should be some default probability that can be adjusted at the collection, campaign or flow level.
         * Flow Engagement Pattern: A way to define engagement over time to tell a story (Spike, Declining, Vanity Trap, etc). Suggest some to me.
      * Publish: This page is where a user could publish the demo data. Publishing demo data means that the data is processed based on a starting date (days ago) and then pushed into a target Demo Org.
         * Output Data:
            * Individual object (required)
            * Related objects (e.g. Case, Web Engagement, etc)
            * Marketing Objects: These are the objects that the demo environment needs to produce data. We'll define these later.
* Demo Orgs: Users should be able to configure one or more demo orgs and subscribe to data from published Demo Collections.
   * Org: Defines the org (demo environment). This is essentially mapping information 

Processing Data (the Engine):
The purpose of the app is to generate data that simulates many Individual's engagement with marketing campaigns. When data is generated, the personas should be "exploded" into Individual profiles by the "Profile Engine." After the Individual profiles are generated, they should be run through the "Engagement Engine" to simulate the engagement.

Some other notes:
* Wherever dates are generated, it would be best to generate a float value that can be converted to a new date when the demo is published to a demo org. That way the data can be easily refreshed.
* Engagement data should be realistic and have jitter so the data isn't uniform.

Stack:
* PostgreSQL database to persist settings
* React
* Tailwind support
* Shadcn UI support
