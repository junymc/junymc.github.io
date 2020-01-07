---
layout: post
title:      "javascript project: Shoe Collection SPA"
date:       2020-01-05 21:10:33 -0500
permalink:  javascript_project_shoe_collection_spa
---

I finally moved another step closer to becoming a programmer. Throughout my time at Flatiron I'd enter every project as if it was the most difficult only to find out the next project would be even harder. However, this javascript project has definitely been the most challenging. Even midway through the project I was still confused and could not understand why I would need certain functions or what some classes do. Hopefully as I go through the process of what I did to complete this application it will better assist with my own understanding javascript because I am having to explain it all to you through my blog entry

## Backend - Rails API
I have two models for this API, the Brand class and Shoe class. Their relationship is based on `has_many` and `belongs_to`. 

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

AJAX stands for Asynchronous JavaScript and XML. AJAX is a new and popular technique for creating more
dynamic and interactive web applications with the help of XML, HTML, CSS, and Java Script.

## Frontend
### HTML
This app starts the page with the basic elements of HTML. Head consists of the style links and the script links to all the files. The Body contains the brand logo imagery and a form to add new shoes. The javascript function creates more elements and the user's action(s) determine what HTML is changed and what will be displayed on the page.

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


I'm a novice with incorporating CSS to create more stylish and interactive pages, but I really enjoy this element of programming because I previously attended school for Graphic Design. Color, font style, and layout selection are a fun part of the design process and I hope to become more knowledgeable and skilled so that I can take advantage of using CSS in future applications.


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

Javascript is the primary code used to display all the elements and functionalities that alter the page based on the user's action. It fetches all the data from the json that is rendered by rails on the backend. It was difficult to understand
the connection between both front and backend , distinction of the different classes in the files. I had to understand how all these connections took place on both sides. 

For purpose of explaination, the `App` class receives and reads information then distributes it to the 
`BrandSelector`class and `DisplayManager` class. These classes create the brand and shoe then render them to display, send POST requests, as well as DELETE requests to the backend through the adapters. 

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

The `BrandAdapter` and `ShoeAdapter` will request POST and DELETE then fetch all the data from the backend based on baseURL(http://localhost:3000/api/v1) This was the most complicated part of the project and I had to repeatedly refer back to Cernan and Micah's videos. Every time I wanted to add additonal functionalities I would have to constantly code back and forth between those files causing me to get lost in the process. 


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



Around the time I added the function to delete a shoe I was able to connect the code between file and the backend without any assistance. This made me really proud to finally make it all work. There were still some small issues I came across that I needed help with, but for the most part I was able to to compete it solo and that was such a great feeling. 

## Conclusion

There was a moment I was depressed and uncertain of continuing to become a programmer. I doubted my ability to complete this course or that I had the necessary talent to be a successful programmer. However, through each project I've had this come across my mind and I continued to overcome these challenges. I found out that I was actually on the right track and I was only making minor mistakes that were causing all this unnecessary frustration. What I've come to realize is that it is okay to make mistakes especially in programming. These mistakes are what require you to dig deep, find the problem, analyze, and form a solution. The basic steps that have made me become a better programmer. 


