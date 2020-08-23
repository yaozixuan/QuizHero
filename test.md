public class UserDao {
    private Sql2o sql2o;
    public UserDao(Sql2o sql2o) {
        this.sql2o = sql2o;
    }

    public static void createRepo(User user) throws IOException {
        String accessToken = GithubUtil.getPersonalAccessToken();
        String org = GithubUtil.getOrganizationName();
        String repoName = user.getRepoId();
        CloseableHttpClient httpclient = HttpClients.createDefault();
        try{
            URI postUri = new URIBuilder()
                    .setScheme("https")
                    .setHost("api.github.com")
                    .setPath("/orgs/" + org + "/repos")
                    .build();
            HttpPost httppost = new HttpPost(postUri);
            String inputJson = "{\n" +
                    "\"name\": \"" + repoName + "\",\n" +
                    "\"private\": \"" + true + "\"\n" +
                    "}";
            System.out.println(inputJson);
            StringEntity stringEntity = new StringEntity(inputJson);
            httppost.setEntity(stringEntity);
            httppost.setHeader("AUTHORIZATION", "token " + accessToken);
            httppost.setHeader("Accept", "application/vnd.github.v3+json");
            HttpResponse response = httpclient.execute(httppost);
            HttpEntity responseEntity = response.getEntity();
            String responseString = EntityUtils.toString(responseEntity);
            System.out.println(responseString);
        } catch (URISyntaxException | UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            httpclient.close();
        }
    }
