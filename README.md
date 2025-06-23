# Developin-Module-Odoo
This guide helps you develop custom modules in Odoo 18, covering everything from setup to deployment. Perfect for developers new to Odoo or creating their first module.

---

#  Developing a Custom Module in Odoo 18 with Python

> This guide helps you **develop custom modules in Odoo 18**, covering everything from setup to deployment. Perfect for developers new to Odoo or creating their first module.

---

##  Table of Contents

* [Requirements](#requirements)
* [Setting Up Odoo 18](#setting-up-odoo-18)
* [Creating Your First Module](#creating-your-first-module)

  * [Module Structure](#module-structure)
  * [Manifest File](#manifest-file)
  * [Model Definition](#model-definition)
  * [Security (Access Rights)](#security-access-rights)
  * [Views (Form & Tree)](#views-form--tree)
  * [Menus & Actions](#menus--actions)
* [Installing the Module](#installing-the-module)
* [Common Errors & Troubleshooting](#common-errors--troubleshooting)
* [Tips & Best Practices](#tips--best-practices)
  

---

##  Requirements

* Python 3.10+
* PostgreSQL
* Git (optional for version control)
* Odoo 18 installed (from source or Docker)
* Basic knowledge of Python & XML
* Code editor (VS Code recommended)

---

##  Setting Up Odoo 18

You can install Odoo 18 from source or via Docker. Here's a simple Docker-based setup:

```bash
docker run -p 8069:8069 --name odoo18 \
  -t odoo:18.0 \
  --addons-path=/mnt/extra-addons \
  -d
```

Make sure to mount your custom addons directory to `/mnt/extra-addons`.

---

##  Creating Your First Module

Let's name your module `my_first_module`.

###  Module Structure

```
my_first_module/
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   └── my_model.py
├── views/
│   └── my_model_views.xml
├── security/
│   ├── ir.model.access.csv
└── README.md
```

---

###  Manifest File

`__manifest__.py` defines your module’s metadata:

```python
{
    'name': 'My First Module',
    'version': '1.0',
    'summary': 'Sample custom module in Odoo 18',
    'description': 'Learn how to create a custom Odoo module',
    'author': 'Your Name',
    'depends': ['base'],
    'data': [
        'security/ir.model.access.csv',
        'views/my_model_views.xml',
    ],
    'installable': True,
    'application': True,
}
```

---

###  Model Definition

Create `models/my_model.py`:

```python
from odoo import models, fields

class MyModel(models.Model):
    _name = 'my.model'
    _description = 'My First Model'

    name = fields.Char(string="Name", required=True)
    description = fields.Text(string="Description")
    active = fields.Boolean(string="Active", default=True)
```

Also update `models/__init__.py`:

```python
from . import my_model
```

---

###  Security (Access Rights)

Create `security/ir.model.access.csv`:

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_my_model,my.model,model_my_model,,1,1,1,1
```

---

###  Views (Form & Tree)

Create `views/my_model_views.xml`:

```xml
<odoo>
    <record id="view_my_model_form" model="ir.ui.view">
        <field name="name">my.model.form</field>
        <field name="model">my.model</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <group>
                        <field name="name"/>
                        <field name="description"/>
                        <field name="active"/>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

    <record id="view_my_model_tree" model="ir.ui.view">
        <field name="name">my.model.tree</field>
        <field name="model">my.model</field>
        <field name="arch" type="xml">
            <tree>
                <field name="name"/>
                <field name="active"/>
            </tree>
        </field>
    </record>
</odoo>
```

---

###  Menus & Actions

Add these lines to `views/my_model_views.xml` after the views:

```xml
<record id="action_my_model" model="ir.actions.act_window">
    <field name="name">My Models</field>
    <field name="res_model">my.model</field>
    <field name="view_mode">tree,form</field>
</record>

<menuitem id="menu_my_model_root" name="My Module"/>
<menuitem id="menu_my_model" name="My Models" parent="menu_my_model_root"/>
<menuitem id="submenu_my_model" name="Items" parent="menu_my_model" action="action_my_model"/>
```

---

##  Installing the Module

1. Copy your module folder to `addons/` or the custom path you set.
2. Restart Odoo server.
3. Activate developer mode.
4. Update App List.
5. Search for **My First Module** and click Install.

---

##  Common Errors & Troubleshooting

| Error Message                           | Cause                                      | Solution                     |
| --------------------------------------- | ------------------------------------------ | ---------------------------- |
| `No module named models`                | Missing or incorrect `__init__.py`         | Add or correct `__init__.py` |
| `Model not found`                       | Wrong `_name` or model not loaded          | Check naming and imports     |
| `Access denied`                         | Missing or incorrect `ir.model.access.csv` | Define correct access rights |
| `Element not found: view_my_model_form` | ID typo in XML                             | Check your XML IDs           |
| Server won't start                      | XML or Python syntax errors                | Use `--log-level=debug`      |

Use this to restart with logs:

```bash
./odoo-bin -d your_db -u my_first_module --log-level=debug
```

---

##  Tips & Best Practices

* Use snake\_case for internal names (`my.model`) and CamelCase for class names (`MyModel`).
* Keep XML readable: indent properly and group related records.
* Avoid hardcoding values in views.
* Use `@api.depends`, `@api.model`, and `@api.onchange` decorators wisely.
* Use `pylint` or `black` for code linting and formatting.

---

### Example Develope Module:

#  Hotel Management Module for Odoo 18

> A full example of a custom module in Odoo 18 that manages hotel rooms, guests, and bookings.

---

##  Module Name: `hotel_management`

###  Key Features

* Manage rooms (number, type, price, status)
* Register guests
* Create and manage bookings (check-in/check-out)
* View current bookings
* Update module without losing data

---

##  Structure

```
hotel_management/
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   ├── hotel_room.py
│   ├── hotel_guest.py
│   └── hotel_booking.py
├── views/
│   ├── hotel_room_views.xml
│   ├── hotel_guest_views.xml
│   └── hotel_booking_views.xml
├── security/
│   ├── ir.model.access.csv
├── data/
│   └── default_data.xml  # Optional
└── README.md
```

---

##  `__manifest__.py`

```python
{
    'name': 'Hotel Management',
    'version': '1.0',
    'summary': 'Manage hotel rooms, guests, and bookings',
    'category': 'Hospitality',
    'author': 'Your Name',
    'depends': ['base'],
    'data': [
        'security/ir.model.access.csv',
        'views/hotel_room_views.xml',
        'views/hotel_guest_views.xml',
        'views/hotel_booking_views.xml',
    ],
    'installable': True,
    'application': True,
}
```

---

##  Models

### 1. `models/hotel_room.py`

```python
from odoo import models, fields

class HotelRoom(models.Model):
    _name = 'hotel.room'
    _description = 'Hotel Room'

    name = fields.Char(string='Room Number', required=True)
    room_type = fields.Selection([
        ('single', 'Single'),
        ('double', 'Double'),
        ('suite', 'Suite')
    ], string='Room Type', required=True)
    price = fields.Float(string='Price per Night')
    is_available = fields.Boolean(string='Available', default=True)
```

---

### 2. `models/hotel_guest.py`

```python
from odoo import models, fields

class HotelGuest(models.Model):
    _name = 'hotel.guest'
    _description = 'Hotel Guest'

    name = fields.Char(string='Full Name', required=True)
    email = fields.Char(string='Email')
    phone = fields.Char(string='Phone Number')
```

---

### 3. `models/hotel_booking.py`

```python
from odoo import models, fields, api

class HotelBooking(models.Model):
    _name = 'hotel.booking'
    _description = 'Hotel Booking'

    guest_id = fields.Many2one('hotel.guest', string='Guest', required=True)
    room_id = fields.Many2one('hotel.room', string='Room', required=True)
    check_in = fields.Date(string='Check-in Date', required=True)
    check_out = fields.Date(string='Check-out Date', required=True)
    total_price = fields.Float(string='Total Price', compute='_compute_total_price', store=True)

    @api.depends('room_id', 'check_in', 'check_out')
    def _compute_total_price(self):
        for rec in self:
            if rec.room_id and rec.check_in and rec.check_out:
                days = (rec.check_out - rec.check_in).days
                rec.total_price = days * rec.room_id.price
            else:
                rec.total_price = 0.0
```

---

##  Access Control

### `security/ir.model.access.csv`

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_hotel_room,access_hotel_room,model_hotel_room,,1,1,1,1
access_hotel_guest,access_hotel_guest,model_hotel_guest,,1,1,1,1
access_hotel_booking,access_hotel_booking,model_hotel_booking,,1,1,1,1
```

---

##  Views

### `views/hotel_room_views.xml`

```xml
<odoo>
  <record id="view_hotel_room_form" model="ir.ui.view">
    <field name="name">hotel.room.form</field>
    <field name="model">hotel.room</field>
    <field name="arch" type="xml">
      <form>
        <sheet>
          <group>
            <field name="name"/>
            <field name="room_type"/>
            <field name="price"/>
            <field name="is_available"/>
          </group>
        </sheet>
      </form>
    </field>
  </record>

  <record id="view_hotel_room_tree" model="ir.ui.view">
    <field name="name">hotel.room.tree</field>
    <field name="model">hotel.room</field>
    <field name="arch" type="xml">
      <tree>
        <field name="name"/>
        <field name="room_type"/>
        <field name="price"/>
        <field name="is_available"/>
      </tree>
    </field>
  </record>

  <record id="action_hotel_room" model="ir.actions.act_window">
    <field name="name">Rooms</field>
    <field name="res_model">hotel.room</field>
    <field name="view_mode">tree,form</field>
  </record>

  <menuitem id="menu_hotel_management" name="Hotel Management"/>
  <menuitem id="menu_hotel_room" name="Rooms" parent="menu_hotel_management" action="action_hotel_room"/>
</odoo>
```

*(Repeat similar structure for guests and bookings.)*

---

##  How to Test & Install

1. Place the folder `hotel_management/` inside your Odoo `addons/` path.
2. Restart Odoo server.
3. Update app list in developer mode.
4. Search for **Hotel Management** and install.
5. Navigate to:

   * **Hotel Management > Rooms**
   * **Hotel Management > Guests**
   * **Hotel Management > Bookings**

---

##  Updating the Module

If you make changes to the Python or XML files:

```bash
./odoo-bin -d your_db_name -u hotel_management
```

For Docker-based installations, enter the container first:

```bash
docker exec -it odoo18 bash
cd /mnt/extra-addons/hotel_management
odoo -u hotel_management -d your_db_name
```

---

##  Common Errors & Fixes

| Error                     | Cause                            | Fix                                  |
| ------------------------- | -------------------------------- | ------------------------------------ |
| `View not found`          | Missing or typo in XML record ID | Check your `id` in XML files         |
| `field not found`         | Missing field in model           | Double-check your field and reload   |
| `Model not defined`       | Not imported in `__init__.py`    | Import model in `models/__init__.py` |
| Compute field not working | Missing `@api.depends()`         | Add correct dependencies in compute  |

---

##  Bonus: Add Room Availability Logic

You can add logic to automatically mark rooms as unavailable when booked using `@api.model.create()` override.

Let me know if you want this logic implemented too.

---

##  Conclusion

This example module gives you a clean, real-world use case of Odoo module development with:

* Models
* Views
* Security
* Custom logic
* Update flow


