# mongo_react_crud

react hooks- use it re use it again and again
use simple hook to change data in dom use a simple hook to get data? no need for react router... npx create-react-app hooks

python : an automated script to retrieve tabular data from a remote db. data should be extracted into csv. start with mysql, then mongodb.

advanced JS promises, async await axios

also repractice BS5 wothout JQ

useEffect

useRef 
 https://www.youtube.com/watch?v=t2ypzz6gJm0&ab_channel=WebDevSimplified

useMemo 
https://www.youtube.com/watch?v=THL1OPn72vo&ab_channel=WebDevSimplified

simple CRUD with hooks
mongo server - > an item with category (display all cats, all items etc.)
useeffect for fetch from the api

build db - blog
post:
_id
name
cat

category:
_id
name
 yarn add mongoose express cors

0. build:
mkdir data controllers logic models
 yarn add mongoose express cors

0. post:
const mongoose = require("mongoose"); const ItemSchema = mongoose.Schema({ name: { type: String, required: true, minlength: 2, maxlength: 30 }, categoryId: { type: mongoose.Schema.Types.ObjectId, ref: "Category", required: true } }, { versionKey: false, toJSON: { virtuals: true }, id: false }); ItemSchema.virtual("category", { ref: "Category", localField: "categoryId", foreignField: "_id", justOne: true }); const Item = mongoose.model("Item", ItemSchema, "items"); module.exports = Item;

0. category:
const mongoose = require("mongoose");
const CategorySchema = mongoose.Schema(
  { name: { type: String, required: true, minlength: 2, maxlength: 40 } },
  { versionKey: false, toJSON: { virtuals: true }, id: false }
);
CategorySchema.virtual("posts", {
  ref: "Post",
  localField: "_id",
  foreignField: "categoryId",
});
const Category = mongoose.model("Category", CategorySchema, "categories");
module.exports = Category;

0. post-logic:
const Post = require("../models/post");
function getAllPosts() {
  return Post.find({}).populate("categories").exec();
}
module.exports = { getAllPosts };

0. category-logic:
const Category = require("../models/category");
function getAllICategories() {
  return Category.find({}).populate("categories").exec();
}
module.exports = { getAllICategories };

0. post ctrl:
const express = require("express");
const router = express.Router();
const Post = require("../models/post");
const PostsLogic = require("../logic/post-logic");
router.get("/", async (request, response) => {
  try {
    const Posts = await PostsLogic.getAllPosts();
    response.json(Posts);
  } catch (error) {
    response.status(500).send(error);
  }
});
module.exports = router;

0. cat ctrl :
const express = require("express");
const router = express.Router();
const Category = require("../models/category");
const catLogic = require("../logic/category-logic");
router.get("/", async (request, response) => {
  try {
    const cats = await catLogic.getAllICategories();
    response.json(cats);
  } catch (error) {
    response.status(500).send(error);
  }
});
module.exports = router;

0. dal:
const mongoose = require("mongoose");
function connectAsync() {
  return new Promise((resolve, reject) => {
    mongoose.connect(
      "mongodb://localhost:27017/blog",
      { useNewUrlParser: true, useUnifiedTopology: true },
      (err, db) => {
        if (err) {
          reject(err);
          return;
        }
        resolve(db);
      }
    );
  });
}
async function connectToDatabase() {
  try {
    const db = await connectAsync();
    console.log("We're connected to " + db.name + " database on MongoDB");
  } catch (err) {
    console.error(err);
  }
}
connectToDatabase();

0. app:
require("./data/dal");
const express = require("express");
const cors = require("cors");
const postsController = require("./controllers/post");
const catsController = require("./controllers//category");
const server = express();
server.use(cors());
server.use(express.json());
server.use("/api/posts", postsController);
server.use("/api/cats", catsController);
server.listen(3000, () => console.log("Listening on http://localhost:3000"));

0. package.json :
{
  "name": "Server",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.17.1",
    "mongoose": "^5.12.7"
  },
  "scripts": {
    "start": "nodemon app.js",
    "git": "git add . && git commit -m",
    "postgit": "git push --all"
  }
}

## COMPLETED CRUD (missing patch and some other smaller requests - recheck):

0. app:
require("./data/dal");
const express = require("express");
const cors = require("cors");
const postsController = require("./controllers/post");
const catsController = require("./controllers//category");
const server = express();
server.use(cors());
server.use(express.json());
server.use("/api/posts", postsController);
server.use("/api/cats", catsController);
server.listen(3000, () => console.log("Listening on http://localhost:3000"));

0. post model:
const mongoose = require("mongoose");
const PostSchema = mongoose.Schema(
  {
    name: { type: String, required: true, minlength: 2, maxlength: 30 },
    categoryId: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Category",
      required: true,
    },
  },
  { versionKey: false, toJSON: { virtuals: true }, id: false }
);
PostSchema.virtual("category", {
  ref: "Category",
  localField: "categoryId",
  foreignField: "_id",
  justOne: true,
});
const Post = mongoose.model("Post", PostSchema, "posts");
module.exports = Post;

0. cat model: 
const mongoose = require("mongoose");
const CategorySchema = mongoose.Schema(
  { name: { type: String, required: true, minlength: 2, maxlength: 40 } },
  { versionKey: false, toJSON: { virtuals: true }, id: false }
);
CategorySchema.virtual("posts", {
  ref: "Post",
  localField: "_id",
  foreignField: "categoryId",
});
const Category = mongoose.model("Category", CategorySchema, "categories");
module.exports = Category;

0. dal (with outsource atals sample):
const mongoose = require("mongoose");
function connectAsync() {
  return new Promise((resolve, reject) => {
    mongoose.connect(
   //    mongoose.connect("mongodb+srv://user:email@gmail.com.mongodb.net/<dbname>?retryWrites=true&w=majority",
      "mongodb://localhost:27017/blog",
      { useNewUrlParser: true, useUnifiedTopology: true },
      (err, db) => {
        if (err) {
          reject(err);
          return;
        }
        resolve(db);
      }
    );
  });
}
async function connectToDatabase() {
  try {
    const db = await connectAsync();
    console.log("We're connected to blog database on MongoDB");
  } catch (err) {
    console.error(err);
  }
}
connectToDatabase();

0. post logic :
const Post = require("../models/post");
function getAllPosts() {
  // return Post.find({}).exec();
  return Post.find({}).populate("categories").exec();
}
function getOnePost(id) {
  // return Post.find({}).populate("categories").exec();
  return Post.find({ _id: id }).exec();
}
function addOnePost(post) {
  return post.save();
}
function updatePost(post) {
  console.log(post._id);
  return new Promise((resolve, reject) => {
    Post.updateOne({ _id: post._id }, post, (err, info) => {
      if (err) {
        reject(err);
        return;
      }
      resolve(info.n ? post : null);
    });
  });
}
module.exports = { getAllPosts, getOnePost, addOnePost, updatePost };

0. cat logic : 
const Category = require("../models/category");
function getAllICategories() {
  return Category.find({}).populate("categories").exec();
}
function getAllIPostsFromCategory(_id) {
  return Category.find({ _id }).populate("posts").exec();
}
module.exports = { getAllICategories, getAllIPostsFromCategory };

0. post ctrl:
const express = require("express");
const router = express.Router();
const Post = require("../models/post");
const postsLogic = require("../logic/post-logic");
router.get("/", async (request, response) => {
  try {
    const Posts = await postsLogic.getAllPosts();
    response.json(Posts);
  } catch (error) {
    response.status(500).send(error);
  }
});
router.get("/post/:id", async (request, response) => {
  const id = request.params.id;
  try {
    const post = await postsLogic.getOnePost(id);
    response.json(post);
  } catch (error) {
    response.status(500).send(error);
  }
});
router.post("/", async (request, response) => {
  const newPost = new Post(request.body);
  try {
    const post = await postsLogic.addOnePost(newPost);
    response.json(post);
  } catch (error) {
    response.status(500).send(error);
  }
});
// PUT http://localhost:3000/api/posts/post/:_id
router.put("/post/:_id", async (request, response) => {
  const postUpdate = new Post(request.body);
  try {
    const post = await postsLogic.updatePost(postUpdate);
    response.json(post);
  } catch (error) {
    response.status(500).send(error);
  }
});
module.exports = router;

0. cat ctrl : 
const express = require("express");
const router = express.Router();
const Category = require("../models/category");
const catLogic = require("../logic/category-logic");
router.get("/", async (request, response) => {
  try {
    const cats = await catLogic.getAllICategories();
    response.json(cats);
  } catch (error) {
    response.status(500).send(error);
  }
});
router.get("/posts/:id", async (request, response) => {
  const id = request.params.id;
  try {
    const cats = await catLogic.getAllIPostsFromCategory(id);
    response.json(cats);
  } catch (error) {
    response.status(500).send(error);
  }
});
module.exports = router;

0. export and import mngo db:
WIN - cmd - run as admin
bin location : cd C:\Program Files\MongoDB\Server\4.4\bin
folder : mongodump -d dbname
in case mongodump isn't installed:
https://www.mongodb.com/try/download/database-tools?tck=docs_databasetools
make sure you install the msi (unless  you want to run mongodump seperately)

## after careful recheck - needs to go over the input update one more time.



coins react:
build a simple bootstrap ui with bs5
don't use JQ
build a simple react app
display the list of 6000 coins in a card grid
display the additional info upon click
create the search bar 

