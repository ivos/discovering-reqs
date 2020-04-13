# Discovering system requirements

**Why would anyone bother with writing FSD on agile project?**

Is Functional Specification Document an obsolete thing of the past? Or is there any point at all to bother with writing it? Is it a documentation chore that adds nothing meaningful to an agile project? Or could it actually help us with developing software?

**TL;DR**

> FSD is a fundamental enabler of team's agility.

Sounds ridiculous? Read on to find out.

---

This is a fictional story.

Although completely made-up, its goal is to illustrate faithfully the process of discovering the system requirements.

It takes a domain that many readers might find familiar and tries to demonstrate eliciting, discovering and exploring user requirements on a system, generally known as "analysis". The perceived familiarity of the domain (taking customer orders) should hopefully make the story more comprehensible, while at the same time demonstrate how fragile such assumptions of familiarity could be.

---

## Day 1

You were just assigned to a new project. Alice is in charge of managing orders in the company and she has asked for a meeting with someone from IT to see if there's a way to help her improve things.

The two of you claim a small conference room and convene there after the lunch.

'So what can I do for you, Alice?' you start it out.

'I was thinking, could we have some sort of overview of pending orders? I mean, most of the time it's OK, but once in a blue moon I feel there's just a bit too many of them.' After a moment of consideration, she adds: 'And very exceptionally, some of them somehow fall through the cracks. You know, I send each new order over to Bob at the fulfillment department as an email attachment, but some email just got lost.'

'Uh-huh,' you fire up you analytical tool that lets you write functional specification in Markdown enhanced with a few keywords that let you generate screen mockups and other things and start drafting it out, 'so there would be sort of a table, listing the orders, right? Let's see. What data do we have in that table? The customer, the order date, what else?'

'Well, what did they order, the quantity and the price, of course.'

'Ok, so that leaves us with something like this.'

```markdown
### Table: Pending orders

- Customer
- Order date
- Goods
- Quantity
- Price
```

'Yeah, looks good to me,' says Alice.

'So then, where do we get the data to show in the table?' you ask.

'Easy, I will enter them into the table.'

'Sure, only as we are talking about a web app, and not an Excel file, we will want to have a form for creating an order.' You change the heading from Table to Form and add a button at the bottom.

```markdown
### Form: Create order

- Customer
- Order date
- Goods
- Quantity
- Price
- [: Create]()
```

'We should also decide on which fields should be required, we probably don't want an order without a customer on it,' you add.

'Sure, all the fields are required then. I would just wish that I would not have to enter the Order date, it should be filled-in automatically, as current date.'

You update the spec accordingly:

```markdown
### Form: Create order

- Customer (required)
- Goods (required)
- Quantity (required)
- Price (required)
- [: Create]()
```

You look at the form and feel something is amiss. Then you realize. 'What if I wanted to order multiple goods on a single order?' you ask Alice.

'No problem, we'll have multiple goods for the order,' says Alice.

'Well then, we'll have to have a way of adding new goods to the order. This form does not allow it. So the goods, quantity and price fields are going to some "order item", rather than the order itself, right?' You create a Table below the Form and move the fields from the Form to become columns in the Table instead, creating an "Add row" button at the bottom of the table as well.

```markdown
### Form: Create order

- Customer (required)
- [: Create]()

### Table: Order items

- Goods (required text)
- Quantity (required text)
- Price (required text)

[: Add row]()
```

'OK, so after filling this in and pressing the Create button, we'll probably want to get to some "Order detail" screen showing the order. Let's have some "sort-of-real" data in there as well, to see it in context,' you continue.

```markdown
### ReadOnlyForm: Order detail

- Customer: Planet express
- Order date: 2 days ago (Feb 20, 2020)

### Table: Order items

- Goods
    - Cigar
    - Beer
    - Pizza
- Quantity
    - 6
    - 12
    - 1
- Price
    - 9.6
    - 4.4
    - 6.5
```

'Looks good,' says Alice.

'Alright, we also need to revisit the "Pending orders" screen,' you say. 'We can no longer have the goods, quantity and price columns there. Since there will be orders with multiple goods, we would not be able to show only a single one of them.'

'But the price should still be there,' says Alice, 'it should show the total price of the order.'

You bring up the Pending orders table again, fix the columns and add some data to make it look more like the real app.

```markdown
### Table: Pending orders

- Customer
    - Planet express
    - MomCorp
    - Planet express
- Order date
    - Feb 20, 2020
    - Feb 18, 2020
    - Feb 9, 2020
- Total
    - 18.5
    - 256
    - 25.78

> Sorted by Order date descending.
```

You've also put in the functional note specifying the sort order of the rows in the table.

Next you draft the current data model to get an idea.

```markdown
## Order

### Attributes:

- Customer (mandatory string): Planet Express
- Order date (mandatory date): 2020-02-20
- Items (1:n `OrderItem`)

## Order item

### Attributes:

- Goods (mandatory string): Beer
- Quantity (mandatory integer): 12
- Price (mandatory decimal): 4.4
```

With that, you both call it a day, content with the progress of the first session.

---

When you are taking the shower that evening, you recall that empty white box labeled Customer on the Create order form. What about repeat business, you think, surely I wouldn't want to re-enter the customer's name every time, when I know they are making an order every Tuesday.

Maybe we could suggest customer names from previous orders as the user starts typing it in.

## Day 2

When pouring juice for the breakfast the next morning, you realize there is also the issue of the goods in the order line items. We should be able to help the user with entering the goods as well, since we surely must already know what we are selling.

We should help with calculating the price as well, as the unit price should also be known, once the item is selected, and then the item price is simply a multiple of the quantity.

---

You meet with Alice on the second session, eager to tell her about the improvements you thought up over night, but she starts first.

'I've checked the emails with the orders I usually send to Bob and I found out we should also have the customer address there so that the fulfillment knows where to send the package. And by the way, it would be great if I could see the history of orders of a given customer.'

That effectively ruins your clever idea of suggesting customer names. The customer now becomes a separate entity which needs to hold its own state apart from the name, namely the address, and there should be a relation between the customer and their orders.

'Okay,' you say, 'so we'll have to have some screens to maintain and review the customers first. Then we can select the correct customer on the order, instead of entering it. Let's draft the "Create customer" screen first.'

```markdown
### Form: Create customer

- Name (R)
- Address (R multiLine)
- [primary: Save]()
```

'The "List customers" screen could then look something like this.'

```markdown
### Form: Find customer

- Name

### Table: Customers

- Name
    - Cookieville Minimum Security Orphanarium
    - Democratic Order of Planets
    - MomCorp
    - Planet express

> Sorted by Name.
```

'And finally, going back to our Create order form, we now see it like this.'

```markdown
### Form: Create order

- Customer (required select: Democratic Order of Planets, MomCorp, Planet express)
- [primary: Create]()
```

'This customer select could then also be on the Pending orders list, enabling us to filter the order records for only those belonging to the selected customer.'

'Sounds all good to me,' approves Alice.

'Next thing is the goods,' you continue. 'I believe it's the same story as with the customers, is it not? We need screens to maintain the goods as well.'

'Actually not,' disagrees Alice, 'there's an Excel file with all our products and when there's a change, I get it from Carol at the purchasing. So I was hoping we could upload this file so that I don't have to type the products in, right?'

'I see, sure, that's the right idea, we don't want to introduce unnecessary typos in, say, the price. So instead, there will be a screen to upload the Excel file of... Wait a sec, did you just called them products instead of goods?'

'Yeah, why not, it's the same thing.'

'I understand,' you say, 'but in the app, we always need to call the same thing with the same name, so as not to confuse the users and also to let the app developers and testers find their bearings.'

'I see. So in that case I'd rather we go with the "product".'

'Alright, so we have the Upload products screen.'

```markdown
### Form: Upload products

- File (R) - Choose the Products Excel file to be uploaded
- [: Upload]()
```

'And the Create order screen becomes...'

```markdown
### Form: Create order

- Customer (R select: Democratic Order of Planets, MomCorp, Planet express)
- [primary: Create]()

### Table: Order items

- Product (R select: Cigar, Beer, Pizza)
- Unit price
    - 0
- Quantity (R text)
- Item price
    - 0

[: Add row]()

> - Unit price taken from the Product selected.
> - Item price = Unit price * Quantity
```

'Which all leads us to the following data model,' you say and draft the new Product and Customer entities and switch the corresponding attributes on Order and Order item to links.

```markdown
## Product
### Attributes:

- Name (M string): Beer
- Unit price (M decimal): 4.4

## Customer
### Attributes:

- Name (M string): Planet Express
- Address (M string): 57th Street, Manhattan, New New York

## Order
### Attributes:

- Customer (n:1 `Customer`)
- Order date (M date): 2020-02-20
- Items (1:n `OrderItem`)

## Order item
### Attributes:

- Product (n:1 `Product`)
- Unit price (M decimal): 1.2
- Quantity (M integer): 12
```

You pause momentarily on the Order item attributes. After a moment of hesitation you add the Unit price there, although there's a link to the Product itself, which already has it, but then what if the product gets updated later on, and the price gets changed? We would like to keep the old orders unchanged in that case and only take the new price for orders created from that point onward. That means we have to duplicate (or denormalize) it onto the Order item.

On the contrary, you vouch against keeping the Item price on the Order item, as it can be easily calculated from other attributes.

You explain your reasoning to Alice, who approves, and you end the meeting going for your regular job routine.

## Day 3

The next day Alice brings Bob to the meeting and explains, 'I was telling Bob about the new Ordering system we are discussing and he thinks that's a cool idea and wanted to join us today.'

'But before we get to Bob,' Alice continues, 'I was thinking about this "denormalization" thing you were talking about yesterday. What if the customer moves and their delivery address changes? And what if they usually want to deliver to their home address, but on one specific occasion they want to deliver to their job?'

'Hmmm, these are some very good questions,' you say. 'If they move, you would probably want to change their address on the customer record, so that next time you create an order for them, the new address will be picked up. We need to keep the older orders intact, however, just as we did with the item price, so we have to duplicate the customer address onto the order.'

'As for the temporarily different address for a special occasion, we would have to let you amend the address on the order manually, which means we need an editable address field on the Create order form.'

'Let's do it. The Create order form becomes...'

```markdown
### Form: Create order

- Customer (R select: Democratic Order of Planets, MomCorp, Planet express)
- Delivery address (R multiLine)
> Pre-filled from `Customer.address`.
- [primary: Create]()
```

'And the Order entity becomes...'

```markdown
## Order
### Attributes:

- Customer (n:1 `Customer`)
- Order date (M date): 2020-02-20
- Delivery address (M string): 57th Street, Manhattan, New New York
- Items (1:n `OrderItem`)
```

'So Bob, how can we help you and the fulfillment department with this new system?' you ask Bob after the address issue is cleared.

'Actually, I really think you can. We are taking the customer orders from Alice, requesting the goods from the warehouse, then packing the parcels and dispatching them via postal service. I would love to have a system that would help us tally the orders we process and make sure we have not forgotten anyone.'

'Uh-huh,' you say, 'so the order has some processing steps. We could model that with a Status attribute that will record the order's progression through them. Can we say what those steps are? From what you say, it looks like the order is first "accepted", that's when Alice creates it and passes it over to you. Then it might be "reserved" when the warehouse has reserved the goods ordered, and finally "dispatched" when you have already sent it out.'

'Yes, I guess that could be,' says Bob.

'And how do you request the products from the warehouse?' you ask.

'Easy, we just send Dan the order number together with the list of goods and quantities.'

'An order number? Did we miss anything here, Alice?' you ask.

'Oh yes, I also assign a number to each order.'

'And how does look?'

'Well, its a year, a month and a number within the month, like "2020-02/202". Do you think the app can take care of assigning the order number as well?'

'Absolutely,' you say, 'no reason why it couldn't. We will just need to make sure to set it up to the correct number when we start using the system, so that we avoid any overlap in the order numbers with orders you have already created manually before that.'

'Great, one less thing to take care of.'

'So that leaves us with the Order entity like this,' you say and quickly add the Status and Order number attributes to it.

```markdown
## Order
### Attributes:

- Order number (naturalKey string): 2020-02/202 - Sequence number within the month.
- Status (M enum: accepted, reserved, dispatched): `accepted`
- Customer (n:1 `Customer`)
- Order date (M date): 2020-02-20
- Delivery address (M string): 57th Street, Manhattan, New New York
- Items (1:n `OrderItem`)
```

'The Create order screen is unchanged,' you continue, 'but we would add a functional note there that the system assigns the new order with Status "accepted" and generates the Order number automatically as a sequence number within the current month.'

```markdown
### Form: Create order

- Customer (R select: Democratic Order of Planets, MomCorp, Planet express)
- Delivery address (R multiLine)
> Pre-filled from `Customer.address`.
- [primary: Create]()

> - Creates new `Order` record.
> - Sets `Order.status` = `accepted`.
> - Sets `Order.orderNumber` = `YYYY-MM/SSS`, where:
>     - `YYYY` is the current year
>     - `MM` is the current month
>     - `SSS` is a unique sequence number within the month
```

'Also, we will have to update the Pending orders screen. It will now become a more generic List orders screen and we add searching by status as well.'

```markdown
### Form: Search orders

- Customer (select: Democratic Order of Planets, MomCorp, Planet express)
- Status (select: Accepted, Reserved, Dispatched)

### Table: List orders

- Order number
    - 2020-02/202
    - 2020-02/198
    - 2020-02/189
- Status
    - Accepted
    - Accepted
    - Dispatched
- Customer
    - Planet express
    - MomCorp
    - Planet express
- Order date
    - Feb 20, 2020
    - Feb 18, 2020
    - Feb 9, 2020
- Total
    - 18.5
    - 256
    - 25.78

> Sorted by Order date descending.
```

'And finally, we need to reflect the new data on the Order detail screen and also add new action buttons for Bob to move the Order forward through its processing steps.'

```markdown
### ReadOnlyForm: Order detail

- Order number: 2020-02/202
- Status: Accepted
- Customer: Planet express
- Order date: 2 days ago (Feb 20, 2020)
- Delivery address: 57th Street, Manhattan, New New York
- [warning: Reserve]()

> Enabled when `Order.status` = `accepted`. Sets `Order.status` = `reserved`.

- [warning: Dispatch]()

> Enabled when `Order.status` = `reserved`. Sets `Order.status` = `dispatched`.

### Table: Order items

- Product
    - Cigar
    - Beer
    - Pizza
- Unit price
    - 1.6
    - 0.36
    - 6.5
- Quantity
    - 6
    - 12
    - 1
- Item price
    - 9.6
    - 4.32
    - 6.5
```

'Whoa, that looks really fabulous, I'm looking forward to start using it,' says Bob.

'Me too!' adds Alice.

'Yes, it's starting to look like we are getting somewhere with this,' you agree. 'I feel the Product upload functionality is ready for development. I'm going to schedule it ASAP. Will keep you posted on how we are doing and invite you to our first demo.'

'The next in line will be the Customer maintenance, which we'll probably give another pass over with Alice the next time we meet to tie up any loose ends first.'

'Meanwhile, all of us may still think the order screens and functionality over to see if we haven't forgotten anything important. As we get confident it's good to go, I'll pass it on to development too.'

## Conclusion

This concludes the story. Since you've made it thus far, I do hope you enjoyed reading it at least as much as I have enjoyed writing it!

We have covered quite some ground here and at least touched on the following subjects:

- **Functional specification** content: UI, Data model, Functionality
- **Screen mockups** as the UI specification, with:
    - Forms and fields
    - Tables and columns
    - Action buttons
- **Data model**
    - Attributes, their status and data types
    - Relations
    - Data normalization and denormalization
- Sample data and it's role in readability of the specification
- The importance of [naming things](https://martinfowler.com/bliki/TwoHardThings.html)
- Entity status as a means of driving its lifecycle

However, if there should be just one thing you take out of this read, I really hope it's this:

> Analyzing system requirements is a process.

It's not a one-time event. There's no way anyone can take a bullet-list of requirements, no matter how detailed they may seem, and with a snap of the fingers make them into the specification of what the system should do.

It takes time, the analytical effort must be duly invested and all the important decisions about what the system should actually do must be decided.

These decisions must be taken in cooperation between someone from the development (the analyst) and someone who drives the product (the product owner). Only representatives of these two entities together are competent to make them.

The decisions unfold themselves over the course of time. Humans need time to process the decisions already made, and these in turn lead to subsequent decisions yet to be made. It's important you walk along this path in due time, if you try to rush it, you usually stray away from it.

I also hope this makes it obvious that the requirements should be analyzed before they are handed over to development. Leaving this task up to programmers is not a good idea, because the product owner must be involved in all those many seemingly "small" decisions. Moreover, the development sprint is not the time for the analytical process to take place. If you do that you will have virtually stalled the development itself, because the team will have to do the analysis and not enough time will be left for the development itself. Not much agility there.

Decouple the analytical process from the development process. Be a few steps ahead with the analysis. Once a feature feels "baked", or reasonably finished, move it to development. That does not mean to freeze the specification at that time, obviously. The spec will always continue to evolve and as it does, the app will have to follow the suit. It only means you will have some real specification of what the system should actually do, and hey, that's not a small feat at all!

Finally, I do hope you have seen that a Functional Specification Document does not have to be a heavy, hundreds of pages long, incomprehensible document that takes months to write and by that time is already outdated. On the contrary, it can actually be quite light-weight, straightforward, readable and live and still be able to facilitate the development (and testing and demo expectations and bug fixing...) very nicely.

Actually, writing the FSD is the fastest way of discovering the true system requirements.

## Next steps

The model specification we have seen being formed in the story is not finished. Even the Product upload feature, which is supposedly ready for development, is not really clear yet. How do we upload the Excel file? We should consider at least the following changes: adding new product, modification of an existing product, deletion of a product (what about order items that refer to it?).

What other improvements do YOU expect to be made?

For actions with substantial functionality it could be beneficial to transform the simplistic functional notes in the screen spec into a use case, which is a more formalized way of describing system functionality. In our spec, the "Create order" and "Upload products" are prime examples of that. Converting those into proper use cases will improve the clarity of the spec.

## The tool

If you think that having an analytical tool that let's you write some simple Markdown text to easily and instantly create screen mockups, specify data model or write use cases is a good idea, you are in luck.

Check out CaseFu, a tool that I have created and used for all the examples in the story, at https://casefu.com/.
