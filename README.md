# BotFramework

Prerequisite
  Java 8
  
  This API is only for Educational purpose
  
  Initialising Bot Builder...
  
    BotBuilder.classPath = request.getRealPath("/"); // Create a ClassPath
		BotBuilder.isLuisApplication = false; // Turns off LUIS Implementation
		
		Message msg = BotBuilder.createBot([Field Class Object comes here],new Message()); // Allocate a New Builder
		
    
  Pass the Message to Bot:
  
    response.setContentType("text/json");
		Message outGoingMessage = BotBuilder.getBotState(request.getReader());
		response.getWriter().println(outGoingMessage.toString());
    
    Where,
      request is the object of HttpServletRequest and
      response is the object of HttpServletResponse
      
      
# Client side
  
