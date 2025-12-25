# Deployment Guide: n8n + WAHA Automation Stack

## Repository
**GitHub**: https://github.com/AbdullahCg/n8n-waha-automation

## Files Included
- `docker-compose.yml` - Complete multi-service orchestration
- - `.env.example` - Environment configuration template
  - - `README.md` - Project overview
    - - `DEPLOYMENT.md` - This file
     
      - ## Quick Start (Local Docker)
     
      - ### Prerequisites
      - - Docker and Docker Compose installed
        - - Git
         
          - ### Installation Steps
         
          - 1. Clone the repository:
            2. ```bash
               git clone https://github.com/AbdullahCg/n8n-waha-automation.git
               cd n8n-waha-automation
               ```

               2. Create environment file:
               3. ```bash
                  cp .env.example .env
                  ```

                  3. Generate secure credentials:
                  4. ```bash
                     # Generate DB password
                     openssl rand -base64 16

                     # Generate WAHA API Key
                     openssl rand -base64 32
                     ```

                     4. Update .env with your values:
                     5. ```bash
                        DB_PASSWORD=your_generated_password
                        WAHA_API_KEY=your_generated_key
                        N8N_HOST=your-domain.com
                        N8N_PROTOCOL=https
                        WEBHOOK_URL=https://your-domain.com
                        ```

                        5. Start the stack:
                        6. ```bash
                           docker compose up -d
                           ```

                           6. Verify services:
                           7. ```bash
                              docker compose ps
                              ```

                              ## Railway Deployment

                              ### Prerequisites
                              - Railway account (https://railway.com)
                              - - GitHub account with access to your fork
                               
                                - ### Deployment Steps
                               
                                - 1. Push this repository to your GitHub account
                                 
                                  2. 2. Go to Railway Dashboard: https://railway.com/dashboard
                                    
                                     3. 3. Click "New Project" → "GitHub Repository"
                                       
                                        4. 4. Select your `n8n-waha-automation` repository
                                          
                                           5. 5. Railway will automatically detect `docker-compose.yml` and deploy services
                                             
                                              6. 6. Configure environment variables in Railway dashboard:
                                                 7.    - DB_PASSWORD
                                                       -    - WAHA_API_KEY
                                                            -    - N8N_HOST
                                                                 -    - N8N_PROTOCOL
                                                                      -    - WEBHOOK_URL
                                                                       
                                                                           - 7. Railway will automatically:
                                                                             8.    - Build Docker images
                                                                                   -    - Deploy PostgreSQL
                                                                                        -    - Deploy n8n service
                                                                                             -    - Deploy WAHA service
                                                                                                  -    - Manage networking
                                                                                                   
                                                                                                       - ### Add Custom Domain
                                                                                                   
                                                                                                       - 1. In Railway Project Settings
                                                                                                         2. 2. Select n8n service
                                                                                                            3. 3. Add domain mapping for N8N_HOST
                                                                                                               4. 4. Set up SSL certificate (automatic with Let's Encrypt)
                                                                                                                 
                                                                                                                  5. ## Service Access
                                                                                                                 
                                                                                                                  6. ### n8n Web Interface
                                                                                                                  7. - URL: https://your-n8n-domain.com
                                                                                                                     - - Port: 5678
                                                                                                                       - - Default credentials: Set on first login
                                                                                                                        
                                                                                                                         - ### WAHA WhatsApp API
                                                                                                                         - - Internal URL (from n8n): http://waha:3000
                                                                                                                           - - External URL: https://your-waha-domain.com:3000
                                                                                                                             - - API Key: From .env WAHA_API_KEY
                                                                                                                              
                                                                                                                               - ### PostgreSQL Database
                                                                                                                               - - Internal: db:5432
                                                                                                                                 - - Username: n8n
                                                                                                                                   - - Database: n8n
                                                                                                                                     - - Password: From DB_PASSWORD
                                                                                                                                      
                                                                                                                                       - ## n8n Configuration
                                                                                                                                      
                                                                                                                                       - ### Connect WAHA to n8n
                                                                                                                                      
                                                                                                                                       - 1. Open n8n dashboard
                                                                                                                                         2. 2. Create new workflow
                                                                                                                                            3. 3. Add HTTP node
                                                                                                                                               4. 4. Configure as follows:
                                                                                                                                                  5.    - Method: GET/POST (as needed)
                                                                                                                                                        -    - URL: http://waha:3000/api/sendMessage
                                                                                                                                                             -    - Headers: X-API-Key: [Your WAHA_API_KEY]
                                                                                                                                                                  -    - Params: Follow WAHA API documentation
                                                                                                                                                                   
                                                                                                                                                                       - ### Create WhatsApp Webhook
                                                                                                                                                                   
                                                                                                                                                                       - 1. In n8n, go to Webhooks
                                                                                                                                                                         2. 2. Create new webhook endpoint: /webhook/waha
                                                                                                                                                                            3. 3. Configure WAHA to send events to: https://your-n8n-domain.com/webhook/waha
                                                                                                                                                                              
                                                                                                                                                                               4. ## Monitoring
                                                                                                                                                                              
                                                                                                                                                                               5. ### View Logs
                                                                                                                                                                               6. ```bash
                                                                                                                                                                                  # All services
                                                                                                                                                                                  docker compose logs -f

                                                                                                                                                                                  # Specific service
                                                                                                                                                                                  docker compose logs -f n8n
                                                                                                                                                                                  docker compose logs -f waha
                                                                                                                                                                                  docker compose logs -f db
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ### Health Checks
                                                                                                                                                                                  ```bash
                                                                                                                                                                                  # n8n health
                                                                                                                                                                                  curl http://localhost:5678/api/v1/health

                                                                                                                                                                                  # WAHA health
                                                                                                                                                                                  curl -H "X-API-Key: YOUR_KEY" http://localhost:3000/api/info
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ## Updating Services
                                                                                                                                                                                  
                                                                                                                                                                                  ### Pull Latest Images
                                                                                                                                                                                  ```bash
                                                                                                                                                                                  docker compose pull
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ### Restart Services
                                                                                                                                                                                  ```bash
                                                                                                                                                                                  docker compose up -d
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ### Graceful Update (Zero Downtime)
                                                                                                                                                                                  ```bash
                                                                                                                                                                                  docker compose pull
                                                                                                                                                                                  docker compose up -d --no-deps --build
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ## Backup & Restore
                                                                                                                                                                                  
                                                                                                                                                                                  ### Backup Database
                                                                                                                                                                                  ```bash
                                                                                                                                                                                  docker exec n8n_db pg_dump -U n8n n8n > backup.sql
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ### Backup Volumes
                                                                                                                                                                                  ```bash
                                                                                                                                                                                  docker run --rm -v n8n_data:/data -v $(pwd):/backup \
                                                                                                                                                                                    alpine tar czf /backup/n8n_data.tar.gz -C /data .
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ### Restore Database
                                                                                                                                                                                  ```bash
                                                                                                                                                                                  cat backup.sql | docker exec -i n8n_db psql -U n8n n8n
                                                                                                                                                                                  ```
                                                                                                                                                                                  
                                                                                                                                                                                  ## Troubleshooting
                                                                                                                                                                                  
                                                                                                                                                                                  ### Services Not Starting
                                                                                                                                                                                  1. Check logs: `docker compose logs`
                                                                                                                                                                                  2. 2. Verify .env values
                                                                                                                                                                                     3. 3. Ensure ports are available
                                                                                                                                                                                       
                                                                                                                                                                                        4. ### Connection Issues Between Services
                                                                                                                                                                                        5. 1. Verify network: `docker network ls`
                                                                                                                                                                                           2. 2. Test connectivity: `docker exec n8n ping waha`
                                                                                                                                                                                             
                                                                                                                                                                                              3. ### Database Connection Failed
                                                                                                                                                                                              4. 1. Verify DB_PASSWORD in .env
                                                                                                                                                                                                 2. 2. Check PostgreSQL logs: `docker compose logs db`
                                                                                                                                                                                                    3. 3. Ensure database is healthy: `docker compose ps`
                                                                                                                                                                                                      
                                                                                                                                                                                                       4. ### Out of Memory
                                                                                                                                                                                                       5. 1. Reduce resource limits in docker-compose.yml
                                                                                                                                                                                                          2. 2. Or increase available system memory
                                                                                                                                                                                                            
                                                                                                                                                                                                             3. ## Security Considerations
                                                                                                                                                                                                            
                                                                                                                                                                                                             4. 1. **Credentials**: All sensitive values in .env (not in repo)
                                                                                                                                                                                                                2. 2. **SSL/TLS**: Always use HTTPS in production
                                                                                                                                                                                                                   3. 3. **API Keys**: Rotate WAHA_API_KEY regularly
                                                                                                                                                                                                                      4. 4. **Database**: Use strong password for DB_PASSWORD
                                                                                                                                                                                                                         5. 5. **Network**: Use Railway's internal networking (no public exposure)
                                                                                                                                                                                                                           
                                                                                                                                                                                                                            6. ## Cost Optimization
                                                                                                                                                                                                                           
                                                                                                                                                                                                                            7. - PostgreSQL: ~$9-15/month on Railway
                                                                                                                                                                                                                               - - n8n service: ~$5/month (pay-as-you-go)
                                                                                                                                                                                                                                 - - WAHA service: ~$5/month (pay-as-you-go)
                                                                                                                                                                                                                                   - - **Total**: ~$15-25/month
                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                     - ## Support
                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                     - For issues or questions:
                                                                                                                                                                                                                                     - 1. Check GitHub Issues
                                                                                                                                                                                                                                       2. 2. Review logs
                                                                                                                                                                                                                                          3. 3. Consult n8n documentation: https://docs.n8n.io
                                                                                                                                                                                                                                             4. 4. WAHA documentation: https://whatsapp-api.hub.docker.com
