# BotFramework

Prerequisite
  Java 8
  Additional JAr Files: https://hc.apache.org/downloads.cgi
  
  This API is only for Educational purpose
  
  Initialising Bot Builder...
  #	You can skip this step if you are not using LUIS
    	IAlgorithmProtocols.mapParameters.put("luisURL",  [srevr ip address...]);
    	IAlgorithmProtocols.mapParameters.put("intentClassPath", "Y:\\EclipseJavaProjects\\LamdasTest\\bin\\ai\\mayank\\Intents");
		
    
 # Pass the Activity to Bot:
  
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.getWriter().append("Served at: ").append(request.getContextPath());
		response.setHeader("Access-Control-Allow-Origin", "*");
		Activity a = new Activity();
		a.contextId = request.getParameter("contextId");
		if(a.contextId == null)	a.contextId = UUID.randomUUID().toString();
		else if(IDialogContext.map.get(a.contextId) != null) {
			IDialogContext context = IDialogContext.map.get(a.contextId);
			context.Done();
		}
		IDialogContext.map.put(a.contextId, null); //create a unique model
		response.getWriter().println(a.toString());
	}

protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setHeader("Access-Control-Allow-Origin", "*");
		BufferedReader reader = request.getReader();
		StringBuilder builder = new StringBuilder();
		String read;
		while((read=reader.readLine())!=null) {
			builder.append(read);
		}
		read = builder.toString();
		
		Activity activity = Activity.createActivity(read);
		IDialogContext context = IDialogContext.map.get(activity.contextId);
		final String uuid = activity.contextId;
		if(context == null || activity.isTaskCompleted){
			context = DialogContext.createContext(null,activity,true); // For using LUIS
			context = DialogContext.createContext([class object],activity,false) //For Single Task
		}		
		context.setActivity(activity);
		System.out.println("ActivitySet");
		synchronized (context) {
			context.notify();
		}
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		activity = context.getActivity();
		activity.contextId = uuid;
		response.getWriter().println(activity.toString());
		IDialogContext.map.put(activity.contextId, context);
	}
	
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
  
