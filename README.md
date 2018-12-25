# mvcpagination

#1. Create the partial _Pagination.cshtml in shared folder inside view folder and add the following code to it

      @{
            var recordsPerPage = Model;
            var totalRecords = ViewBag.TotalRecords==null? 0 : ViewBag.TotalRecords;
            int numberOfButtons;
            if(totalRecords%recordsPerPage==0){
                numberOfButtons = totalRecords/recordsPerPage;
            } else {
                numberOfButtons = (totalRecords/recordsPerPage)+1;
            }
        }
        <ul class="pagination">
        @if(ViewBag.CurrentPage>1){
            <li>
                <a asp-route-page=@(ViewBag.CurrentPage-1)>Prev</a>
            </li>
        }

        @for(int i =0 ; i<numberOfButtons; i++){
            @if(ViewBag.CurrentPage<numberOfButtons){
            <li>
                <a asp-route-page="@(ViewBag.CurrentPage+i)">@(ViewBag.CurrentPage+i)</a>
            </li>
            }
        }
        @if(ViewBag.CurrentPage<numberOfButtons){
            <li>
                <a asp-route-page=@(ViewBag.CurrentPage+1)>Next</a>
            </li>
        }
        </ul>
        
#2. Create base controller and add the following code to it

      public List<T> PaginatedResult(List<T> t, int page, int rowsPerPage)
          {
              @ViewBag.TotalRecords = t.Count;
              @ViewBag.CurrentPage = page;

              var skip = (page - 1) * rowsPerPage;

              var paginatedResult = t.Skip(skip).Take(rowsPerPage).ToList();
              return paginatedResult;
          }
    
    
  #3. Call the PaginatedResult method of base controller from your controller where you want to paginate your result as below:
      
      public IActionResult Index(string search = null, int page=1){
             var jobs = _jobRepository
                        .Search(x=>x.JobTitle.Contains(search) ||search == null)
                        .OrderByDescending(x=>x.CreatedAt).ToList();

            var paginatedResult = PaginatedResult(jobs,page,10);

            return View(paginatedResult);
        }
        
 #4. Use pagination in your view as below:
  @await Html.PartialAsync("_Pagination",10)
  
        
