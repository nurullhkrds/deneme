import org.springframework.data.jpa.domain.Specification;
import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Predicate;
import javax.persistence.criteria.Root;

public class ReturnMapSpecification {

    public static Specification<ReturnMap> hasReturnMapCode(String returnMapCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (returnMapCode == null) {
                return cb.conjunction();
            }
            return cb.equal(root.get("returnMapCode"), returnMapCode);
        };
    }

    public static Specification<ReturnMap> hasAaErrorCode(String aaErrorCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (aaErrorCode == null) {
                return cb.conjunction();
            }
            return cb.equal(root.get("aaErrorCode"), aaErrorCode);
        };
    }

    public static Specification<ReturnMap> hasInstitutionErrorCode(String institutionErrorCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (institutionErrorCode == null) {
                return cb.conjunction();
            }
            return cb.equal(root.get("institutionErrorCode"), institutionErrorCode);
        };
    }
}







 @GetMapping("/search")
    public List<ReturnMap> searchReturnMaps(
            @RequestParam(required = false) String returnMapCode,
            @RequestParam(required = false) String aaErrorCode,
            @RequestParam(required = false) String institutionErrorCode) {
        return returnMapService.searchReturnMaps(returnMapCode, aaErrorCode, institutionErrorCode);
    }













import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class ReturnMapController {

    @Autowired
    private ReturnMapService returnMapService;

    @GetMapping("/search")
    public List<ReturnMap> searchReturnMaps(
            @RequestParam(required = false) String returnMapCode,
            @RequestParam(required = false) String aaErrorCode,
            @RequestParam(required = false) String institutionErrorCode) {
        return returnMapService.searchReturnMaps(returnMapCode, aaErrorCode, institutionErrorCode);
    }
}
