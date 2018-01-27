# BotFramework

Prerequisite
  Java 8
  Additional JAr Files: https://hc.apache.org/downloads.cgi
  
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
      
      
# Client side code

	public static void main(String[] args) throws ClientProtocolException, IOException {
		String UUID = "";
		Scanner s = new Scanner(System.in);
		Gson gson = new Gson();
		String url = "http://localhost:8085/BotFramework_V1/ServletClass?"; // Server URL Address
		HttpClient client = HttpClients.createDefault();
		HttpGet get = new HttpGet(url);
		Message activity = new Message();		
		HttpResponse response = client.execute(get);
		String json = EntityUtils.toString(response.getEntity());
		System.out.println(json);		
		activity = gson.fromJson(json,Message.class);
		UUID = activity.context_id;
		while(true){
			if(activity.fieldType!=null && activity.fieldType.equals(FieldType.Completed)){
				get = new HttpGet(url);
				activity = new Message();		
				response = client.execute(get);
				json = EntityUtils.toString(response.getEntity());
				activity = gson.fromJson(json,Message.class);
				UUID = activity.context_id;
				System.out.println(json);
			}
			activity.text = s.nextLine();
			if(activity.fieldType !=null){
				if(activity.fieldType.equals(FieldType.EnumList)){
						activity.requestArray = Arrays.asList(activity.text.split(";"));
				}
			}
			activity.fieldValue = activity.text;
			HttpPost post = new HttpPost(url);
			String t = gson.toJson(activity);
			System.out.println(t);
			post.setEntity(new StringEntity(t));
			response = client.execute(post);
			json = EntityUtils.toString(response.getEntity());
			activity = gson.fromJson(json, Message.class);
			System.out.println(json);
		}
	}
  
