# Request-en-Perfecto-Android

```java
// Instantiate the RequestQueue.
        queueTrackingCode = Volley.newRequestQueue(SubListItemActivity.this);
        String url = "URL";

        // Request a string response from the provided URL.
        StringRequest stringRequest = new StringRequest(Request.Method.POST, url,
                new Response.Listener<String>() {
                    @Override
                    public void onResponse(String response) {
                        Log.d(TAG," ->>>>> "+response);

                        try {
                            JSONObject responseRaw = new JSONObject(response);
                            JSONObject res = responseRaw.getJSONObject("response");

							ListItemDetail listItemDetail = new ListItemDetail(res);

							String listaID = String.valueOf(res.getInt("id_lista"));

							listItemDetail.setLista_id(listaID);

							if (res.has("requerir_firma") && res.getInt("requerir_firma") == 1){
								listItemDetail.setSinged(true);
							}

							if (res.has("requerir_cedula") && res.getInt("requerir_cedula") == 1){
								listItemDetail.setIdRequired(true);
							}

                            ItemDetailActivity.selectedData = listItemDetail;
                            Intent intent = new Intent(mContext, ItemDetailActivity.class);
                            startActivityForResult(intent, 123);

                        }catch (JSONException e){
                            AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), e.getMessage() + " #507");
                        }
                    }
                },

                new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {

                        showLoading(false);

						if(error instanceof NoConnectionError) {
							AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), "No hay internet.");
						}
						else if(error instanceof TimeoutError){
							AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), "Tiempo de ejecución terminado, intentar nuevamente por favor.");
						} else if (error instanceof AuthFailureError) {

							// Para ejecutarse el servidor debe retornar 401

							AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), "Error en la conexion al servidor.");

						} else if (error instanceof ServerError) {

							try {
								String responseBody = new String(error.networkResponse.data, "utf-8");

								JSONObject data = new JSONObject(responseBody);

								if (data.has("response")){
                                    AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), data.getString("response") +
                                            " Tracking code capturado: ("+trackingCode+") Si la lectura esta mal, favor intentar nuevamente.");
								}

								Log.d(TAG, " onErrorResponse: "+data.toString());
							}
							catch (JSONException e){
								e.printStackTrace();
							}
							catch (UnsupportedEncodingException e) {
								e.printStackTrace();
							}

						} else if (error instanceof NetworkError) {

							AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), "El Firewall no permite realizar la solicitud.");

						} else if (error instanceof ParseError) {

							AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), "Error al parsear la información retornada por el servidor.");
						}
						else{

							// Error general
							AppUtil.showErrorDialog(mContext, getString(R.string.lab_error), "Hemos tenido un problema, por favor intentar nuevamente.");
						}

						Log.d(TAG, "onErrorResponse:  -> "+error.getMessage());
					}
                }){

            @Override
            protected Map<String,String> getParams(){
                Map<String,String> params = new HashMap<>();

                params.put("key", value);
                params.put("key", value);

                return params;
            }

            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String,String> params = new HashMap<>();

                params.put("Content-Type","application/x-www-form-urlencoded");

                return params;
            }
        };

        // Add Tag
        stringRequest.setTag("login");

        // Add the request to the RequestQueue.
        queueTrackingCode.add(stringRequest);
		
```
