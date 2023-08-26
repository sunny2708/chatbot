# chatbot
1.WE WRITE CODE FOR dbconfig.js
module.exports = {
    HOST: "localhost",
    USER: "root",
    PASSWORD: '',
    DB: 'node_sequelize_api_db',
    dialect: "mysql",
    pool: {
      max: 5,
      min: 0,
      acquire: 30000,
      idle: 10000
    }
  };
Define Models:
Inside the models folder, define the Sequelize models for your entities (User, Chatbot, Conversation, EndUser) and Index.js
1.User.js
const { DataTypes } = require('sequelize');
const sequelize = require('server.js');

const User = sequelize.define('user', {
  username: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
  // other fields...
});

module.exports = User;

2.chatbot.js
const { DataTypes } = require('sequelize');
const sequelize = require('server.js');
const User = require('./user');

const Chatbot = sequelize.define('chatbot', {
  name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  
});

Chatbot.belongsTo(User);
User.hasMany(Chatbot);

module.exports = Chatbot;

3.Enduser
const { DataTypes } = require('sequelize');
const sequelize = require('server.js');

const EndUser = sequelize.define('endUser', {
  name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
  
});

module.exports = EndUser;

4.conversation
const { DataTypes } = require('sequelize');
const sequelize = require('server.js');
const Chatbot = require('./chatbot');

const Conversation = sequelize.define('Conversation', {
  // fields...
});

Conversation.belongsTo(Chatbot);
Chatbot.hasMany(Conversation);

module.exports = Conversation;

5.Index.js
const dbConfig = require('../config/dbConfig.js');

const {Sequelize, DataTypes} = require('sequelize');

const sequelize = new Sequelize(
    dbConfig.DB,
    dbConfig.USER,
    dbConfig.PASSWORD, {
        host: dbConfig.HOST,
        dialect: dbConfig.dialect,
        operatorsAliases: false,

        pool: {
            max: dbConfig.pool.max,
            min: dbConfig.pool.min,
            acquire: dbConfig.pool.acquire,
            idle: dbConfig.pool.idle

        }
    }
)

sequelize.authenticate()
.then(() => {
    console.log('connected..')
})
.catch(err => {
    console.log('Error'+ err)
})

const db = {}

db.Sequelize = Sequelize
db.sequelize = sequelize
db.Sequelize = Sequelize
db.sequelize = sequelize

db.chatbots = require('./chatbot.js')(sequelize, DataTypes)
db.conversations = require('./conversation.js')(sequelize, DataTypes)
db.endusers = require('./enduser.js')(sequelize, DataTypes)
db.users = require('./user.js')(sequelize, DataTypes)

db.sequelize.sync({ force: false })
.then(() => {
    console.log('yes re-sync done!')
})

module.exports = db

Define Controller Folder:
Inside the controller folder(User, Chatbot, Conversation, EndUser) 
1.User
const User = require('../models/user');

const UserController = {
  createUser: async (req, res) => {
    try {
      const user = await User.create(req.body);
      res.status(201).json(user);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  },

  getUsers: async (req, res) => {
    try {
      const users = await User.findAll();
      res.json(users);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  getUserById: async (req, res) => {
    const userId = req.params.id;
    try {
      const user = await User.findByPk(userId);
      if (user) {
        res.json(user);
      } else {
        res.status(404).json({ message: 'User not found' });
      }
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  updateUser: async (req, res) => {
    const userId = req.params.id;
    try {
      const user = await User.findByPk(userId);
      if (user) {
        await user.update(req.body);
        res.json(user);
      } else {
        res.status(404).json({ message: 'User not found' });
      }
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  },

  deleteUser: async (req, res) => {
    const userId = req.params.id;
    try {
      const user = await User.findByPk(userId);
      if (user) {
        await user.destroy();
        res.json({ message: 'User deleted successfully' });
      } else {
        res.status(404).json({ message: 'User not found' });
      }
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },
};

module.exports = UserController;
2.EndUser
const EndUser = require('../models/enduser');

const EndUserController = {
  registerEndUser: async (req, res) => {
    try {
      const endUser = await EndUser.create(req.body);
      res.status(201).json(endUser);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  },

  getEndUsers: async (req, res) => {
    try {
      const endUsers = await EndUser.findAll();
      res.json(endUsers);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  getEndUserById: async (req, res) => {
    const endUserId = req.params.endUserId;
    try {
      const endUser = await EndUser.findByPk(endUserId);
      if (endUser) {
        res.json(endUser);
      } else {
        res.status(404).json({ message: 'EndUser not found' });
      }
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  updateEndUser: async (req, res) => {
    const endUserId = req.params.endUserId;
    try {
      const endUser = await EndUser.findByPk(endUserId);
      if (endUser) {
        await endUser.update(req.body);
        res.json(endUser);
      } else {
        res.status(404).json({ message: 'EndUser not found' });
      }
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  },

  deleteEndUser: async (req, res) => {
    const endUserId = req.params.endUserId;
    try {
      const endUser = await EndUser.findByPk(endUserId);
      if (endUser) {
        await endUser.destroy();
        res.json({ message: 'EndUser deleted successfully' });
      } else {
        res.status(404).json({ message: 'EndUser not found' });
      }
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },
};

module.exports = EndUserController;

3.Conversation
const Conversation = require('../models/conversation');
const Chatbot = require('../models/chatbot');

const ConversationController = {
  startConversation: async (req, res) => {
    const chatbotId = req.params.chatbotId;
    try {
      const chatbot = await Chatbot.findByPk(chatbotId);
      if (!chatbot) {
        res.status(404).json({ message: 'Chatbot not found' });
        return;
      }

      const conversation = await Conversation.create({
        ...req.body,
        ChatbotId: chatbotId,
      });
      res.status(201).json(conversation);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  },

  getConversationsForChatbot: async (req, res) => {
    const chatbotId = req.params.chatbotId;
    try {
      const chatbot = await Chatbot.findByPk(chatbotId);
      if (!chatbot) {
        res.status(404).json({ message: 'Chatbot not found' });
        return;
      }

      const conversations = await Conversation.findAll({
        where: { ChatbotId: chatbotId },
      });
      res.json(conversations);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  getConversationById: async (req, res) => {
    const conversationId = req.params.conversationId;
    try {
      const conversation = await Conversation.findByPk(conversationId);
      if (conversation) {
        res.json(conversation);
      } else {
        res.status(404).json({ message: 'Conversation not found' });
      }
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  updateConversation: async (req, res) => {
    const conversationId = req.params.conversationId;
    try {
      const conversation = await Conversation.findByPk(conversationId);
      if (conversation) {
        await conversation.update(req.body);
        res.json(conversation);
      } else {
        res.status(404).json({ message: 'Conversation not found' });
      }
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
},
};

4.Chatbot
const Chatbot = require('../models/chatbot');
const User = require('../models/user');

const ChatbotController = {
  createChatbot: async (req, res) => {
    const userId = req.params.userId;
    try {
      const user = await User.findByPk(userId);
      if (!user) {
        res.status(404).json({ message: 'User not found' });
        return;
      }

      const chatbot = await Chatbot.create({
        ...req.body,
        UserId: userId,
      });
      res.status(201).json(chatbot);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  },

  getChatbotsForUser: async (req, res) => {
    const userId = req.params.userId;
    try {
      const user = await User.findByPk(userId);
      if (!user) {
        res.status(404).json({ message: 'User not found' });
        return;
      }

      const chatbots = await Chatbot.findAll({
        where: { UserId: userId },
      });
      res.json(chatbots);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  getChatbotById: async (req, res) => {
    const chatbotId = req.params.chatbotId;
    try {
      const chatbot = await Chatbot.findByPk(chatbotId);
      if (chatbot) {
        res.json(chatbot);
      } else {
        res.status(404).json({ message: 'Chatbot not found' });
      }
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },

  updateChatbot: async (req, res) => {
    const chatbotId = req.params.chatbotId;
    try {
      const chatbot = await Chatbot.findByPk(chatbotId);
      if (chatbot) {
        await chatbot.update(req.body);
        res.json(chatbot);
      } else {
        res.status(404).json({ message: 'Chatbot not found' });
      }
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  },

  deleteChatbot: async (req, res) => {
    const chatbotId = req.params.chatbotId;
    try {
      const chatbot = await Chatbot.findByPk(chatbotId);
      if (chatbot) {
        await chatbot.destroy();
        res.json({ message: 'Chatbot deleted successfully' });
      } else {
        res.status(404).json({ message: 'Chatbot not found' });
      }
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  },
};

module.exports = ChatbotController;

Define Router Folder:
Inside the router folder, (User, Chatbot, Conversation, EndUser)
1.User
const express = require('express');
const UserController = require('../controller/usercontroller');

const router = express.Router();

router.post('/users', UserController.createUser);
router.get('/users', UserController.getUsers);
router.get('/users/:id', UserController.getUserById);
router.put('/users/:id', UserController.updateUser);
router.delete('/users/:id', UserController.deleteUser);

module.exports = router;

2.Enduser
const express = require('express');
const EndUserController = require('../controllers/EndUserController');

const router = express.Router();

router.post('/endusers', EndUserController.registerEndUser);
router.get('/endusers', EndUserController.getEndUsers);
router.get('/endusers/:endUserId', EndUserController.getEndUserById);
router.put('/endusers/:endUserId', EndUserController.updateEndUser);
router.delete('/endusers/:endUserId', EndUserController.deleteEndUser);

module.exports = router;

3.Conversation
const express = require('express');
const ConversationController = require('../controllers/ConversationController');

const router = express.Router();

router.post('/chatbots/:chatbotId/conversations', ConversationController.startConversation);
router.get('/chatbots/:chatbotId/conversations', ConversationController.getConversationsForChatbot);
router.get('/conversations/:conversationId', ConversationController.getConversationById);
router.put('/conversations/:conversationId', ConversationController.updateConversation);
router.delete('/conversations/:conversationId', ConversationController.deleteConversation);

module.exports = router;

4.Chatbot
const express = require('express');
const ChatbotController = require('../controllers/ChatbotController');

const router = express.Router();

router.post('/users/:userId/chatbots', ChatbotController.createChatbot);
router.get('/users/:userId/chatbots', ChatbotController.getChatbotsForUser);
router.get('/chatbots/:chatbotId', ChatbotController.getChatbotById);
router.put('/chatbots/:chatbotId', ChatbotController.updateChatbot);
router.delete('/chatbots/:chatbotId', ChatbotController.deleteChatbot);

module.exports = router;

Create Server.js file
const express = require("express");
const cors = require("cors");

const app = express();

var corsOptions = {
  origin: "http://localhost:8081"
};

app.use(cors(corsOptions));


app.use(express.json());


app.use(express.urlencoded({ extended: true }));

app.use('/api/users', require('./routes/userroutes.js'));
app.use('/api/users/:userId/chatbots', require('./routes/chatbotroutes.js'));
app.use('/api/chatbots/:chatbotId/conversations', require('./routes/conversationroutes.js'));
app.use('/api/endusers', require('./routes/enduserroutes.js'));




app.get("/", (req, res) => {
  res.json({ message: "Welcome to application." });
});


const PORT = process.env.PORT || 8080;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}.`);
});


