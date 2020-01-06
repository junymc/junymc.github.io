---
layout: post
title:      "javascript project: Shoe Collection SPA"
date:       2020-01-05 21:10:33 -0500
permalink:  javascript_project_shoe_collection_spa
---

Finally I moved another step closer to become a programmer! Everytime I do my project, I feel like "This is the hardest one!" but after I start a new project, the hardest one changes to the new one. Not because I said that, this javascript project is definately the hartest one. Even though while I was in the half way, I was still confused and wasn't sure why I needed this function, what these classes do... Hopefully, I can understand more about javascript after I go over about my project writing this blog.

## Backend - Rails API
I have two models for this API. Brand class and Shoe class. Their relationship is `has_many` and `belongs_to`. 

```
class Brand < ApplicationRecord
    has_many :shoes
end

class Shoe < ApplicationRecord
    belongs_to :brand

    validates :model, :size, :color, :image, presence: true
end
```

The controllers have some basic CRUD actions render to json. 
```
class Api::V1::BrandsController < ApplicationController
    def index
        @brands = Brand.all

        render json: @brands, status: 200
    end

    def show
        @brand = Brand.find(params[:id])

        render json: @brand, status: 200
    end

    def create
        @brand = Brand.new(brand_params)
        if @brand.save
            render json: @brand, status: 200
        else
            render :new
        end
    end
		
	 def destroy
        @brand = Brand.find(params[:id])
        @brand.delete

        render json: {brandId: @brand.id}
    end

    private
    def brand_params
        params.require(:brand).permit(:name, :image)
    end

end
```

This APP uses Rack CORS for handling Cross-Origin Resource Sharing (CORS), making cross-origin AJAX possible.
```
gem 'rack-cors'
```

AJAX stands for Asynchronous JavaScript and XML. AJAX is a new and popular technique for creating better, faster, and more interactive web applications with the help of XML, HTML, CSS, and Java Script.

## Frontend
### HTML
This app starts the page with basic elements in HTML. Head with style links and script links to all the files. Body with all the brand logo images and new shoe form. As the javascript function creates more elemnts and depends the user's action, the HTML will change and so does the page.

```
<div id="new-shoe-container">
      <form id="new-shoe-form">
        <h3>Add a new shoe</h3>
        Brand:
          <select name="brand_id" id="shoe-brand-name" form="new-shoe-form">
            
          </select>
        Model: <input type="text" name="model" id="new-shoe-model">
        Size: <input type="text" name="size" id="new-shoe-size">
        Color: <input type="text" name="color" id="new-shoe-color">
        Category:
          <select name="category" id="new-shoe-category" form="new-shoe-form">
            <option value="Boots">Boots</option>
            <option value="Heels">Heels</option>
            <option value="Sneakers">Sneakers</option>
            <option value="Trainers">Trainers</option>
            <option value="Sandals">Sandals</option>
            <option value="Wedges">Wedges</option>
            <option value="Flats">Flats</option>
          </select>
        Image: <input type="text" name="image" id="new-shoe-image">
        <button type="submit" class="btn btn-primary">Save</button>
          </form>
    <br>
    </div>
```

### CSS
I'm still new to making fancy, stylish web page but I love this part mostly! Since I already finished a course for Graphic Design, changing colors of elements, choosing a font style and deciding layouts are so fun work to me. I'm not good at it yet but I really like to know more about it till I'm good!

```
button {
  font-size: 15px;
  display: inline-block;
  border: 1px solid rgb(155, 152, 152);
  padding: 0 10px;
  border-radius: 10px;
  text-decoration: none;
  height: 30px;
  line-height: 5px;
  margin-top: 20px;
  cursor: pointer;
  font-weight: 300;
  margin-top: 15px;
}

button:hover {
    border: 1px solid rgb(250, 247, 249);
    background-color: rgb(248, 169, 176);
    color: white;
}
```

### Javascript
So the javascript is the main part that displays all the elements and has the functionalities that change the page by user's action. It fetches all the data from the json that rendered by rails backend side. First, it was hard to understand the connection between the backend and frontend, also between the different classes in the different files. I had to make sure that they all know what is going on the other side.

`App` class is the main guy who gets and reads the informations and give them out  to `BrandSelector` class and `DisplayManager` class. These two guys will create the brand and shoe, render them to display and send POST request, as well as DELETE request to the backend through the adapters.

```
class BrandSelector {
    constructor() {
        this.brands = []
        this.bindingsAndEventListeners()       
        this.selectedBrand = null
    }

    bindingsAndEventListeners() {
        this.container = document.querySelector('#brand-container')
        this.allBrandsContainer = document.getElementById("all-brands")
        this.image = document.getElementsByTagName('img')

        this.allBrandsContainer.addEventListener('click', this.selectBrandLogo.bind(this))
    }
	}
	
	class DisplayManager {
    constructor() {
        this.shoes = []
        this.selectedBrand = null
        this.bindingsAndEventListeners()
    }

    async fetchAndRenderShoes() {
      
        try {
            console.log(this.selectedBrand)
            const brandId = this.selectedBrand.id
            this.shoes = await Shoe.retrieveByBrand(brandId)
            this.renderShoes()
        }catch(err) {
            alert(err)
        }
        
    }
	}
```

`BrandAdapter` and `ShoeAdapter`  will request POST and DELETE then fetch all the data from the rail side based on baseURL(http://localhost:3000/api/v1). This part is the most complecated in the project... I've watched Cernan and Micah's videos over and over during this project. Evrytime there's new funtionality, I had to code back and forth in between those files and sometimes I got lost what I am doing.

```
class BrandAdapter {

        get baseURL() {
            return  `http://localhost:3000/api/v1`
        }

        get brandsURL() {
            return `${this.baseURL}/brands`
        }

        brandURL(id) {
            return `${this.brandsURL}/${id}`
        }

        get headers() {
            const stdHeader = {
                'Content-Type': 'appliaction/json',
                'Accept': 'application/json'
            }
            return stdHeader
        }
				
			async newBrand(params) {
            const res = await fetch(this.brandsURL,{
                method: 'POST',
                headers: this.headers,
                body: JSON.stringify(params)
            })
            this.checkStatus(res)
            return await res.json()
        }
			}
```

After about the itme that I had to add this delete button for my shoe, I was able to connect the code between files and with the backend without help. I was so proud of myself that I made it work!! Well, there was a little issues and had to get some help later on but I made it work mostly by myself. That was awesome. 

## Conclusion
I had a depressed momment about becoming a programmer. I doubted about myself if I can really finish this course and become a programmer successfully. Not because it was hard, I was thinking that I might not have the ablity or tanlented with coding. But I believe in myself now that I can do this and I like acomplish things that challenge me!  A lot of times I realized that I wasn't sure about what I did during this project or even other projects. But I found out I was in the right track and I just needed to fix a little part not completely wrong. That's probably I need to work on. Don't be afraid to be wrong, I can always learn from mistakes and get better!
